# Clinical Analytics Guide

Working examples for computing clinical analytics from CNS archive data. All examples assume data has been loaded via `cns-utils`.

---

## Rolling Average

Smooths a time series over a configurable window. Commonly applied to ICP, MAP, or CPP trends to reduce noise.

```python
import numpy as np
import pandas as pd
from cns_utils import CNSDataSource, read_time_series_data
from moberg_dsp.preprocessing import resample, get_next_aligned_timestamp
from moberg_dsp.epoching import rolling_windows

ds = CNSDataSource("/data/patients/Patient_001")
icp = read_time_series_data("ICP,Mean,Numeric,Float,CNSMonitor", ds, False,
                            ds.start_time(), ds.end_time())

# Resample to 1-second grid
start_ts = pd.Timestamp(ds.start_time(), unit="us")
end_ts = pd.Timestamp(ds.end_time(), unit="us")
icp_1s = resample(icp, resample_duration_ms=1000,
                  min_timestamp=start_ts, max_timestamp=end_ts)

# 10-minute rolling average, computed every 1 minute
window_samples = 600   # 10 min * 60 sec
step_samples = 60      # 1 min * 60 sec
windows, index = rolling_windows(icp_1s, window=window_samples, step=step_samples)
rolling_avg = pd.Series(np.nanmean(windows, axis=1), index=index)
```

**Parameters:**
- `window`: Window duration in samples (at the resampled rate)
- `step`: How often to compute a new value, in samples

---

## PRx — Pressure Reactivity Index

Rolling Pearson correlation between Mean Arterial Blood Pressure (ABP) and Intracranial Pressure (ICP). Measures cerebral pressure autoregulation.

```python
from datetime import timedelta
from cns_utils import CNSDataSource, read_time_series_data
from moberg_dsp.analytics.PRx import prx

ds = CNSDataSource("/data/patients/Patient_001")
abp = read_time_series_data("ABP,Mean,Numeric,Float,IntelliVue", ds, False,
                            ds.start_time(), ds.end_time())
icp = read_time_series_data("ICP,Mean,Numeric,Float,CNSMonitor", ds, False,
                            ds.start_time(), ds.end_time())

prx_series = prx(
    abp, icp,
    resample_freq=timedelta(seconds=10),   # Resample both signals to 10s
    window_size=timedelta(minutes=5),      # 5-minute correlation window
    step_size=timedelta(minutes=1)         # Compute every 1 minute
)
# prx_series: pd.Series with DatetimeIndex, values in [-1, 1]
```

**Interpretation:**
| PRx Range | Meaning |
|-----------|---------|
| < 0 | Intact autoregulation — ICP decreases when ABP rises |
| 0 to 0.25 | Borderline |
| > 0.25 | Impaired autoregulation — ICP passively follows ABP |

**Parameters:**
- `resample_freq`: Both signals resampled to this interval before correlation (default: 10s)
- `window_size`: Correlation window duration (default: 5 min)
- `step_size`: How often a new PRx value is computed (default: 1 min)

---

## COx — Cerebral Oximetry Index

Rolling correlation between ABP and regional cerebral oxygen saturation (rSO2/StO2). Measures cerebrovascular oxygenation reactivity. Uses averaged bilateral rSO2 when both hemispheres are available.

```python
from datetime import timedelta
from cns_utils import CNSDataSource, read_time_series_data
from moberg_dsp.analytics.COx import cox

ds = CNSDataSource("/data/patients/Patient_001")
abp = read_time_series_data("ABP,Mean,Numeric,Float,IntelliVue", ds, False,
                            ds.start_time(), ds.end_time())
sto2_left = read_time_series_data("rSO2,Left,Numeric,Float,INVOS", ds, False,
                                  ds.start_time(), ds.end_time())
sto2_right = read_time_series_data("rSO2,Right,Numeric,Float,INVOS", ds, False,
                                   ds.start_time(), ds.end_time())

cox_series = cox(
    abp, sto2_left, sto2_right,
    resample_freq=timedelta(seconds=10),
    window_size=timedelta(minutes=5),
    step_size=timedelta(minutes=1)
)
```

If only one hemisphere is available, pass an empty `pd.Series(dtype=float)` for the missing side.

**Interpretation:** Same thresholds as PRx. COx > 0.25 suggests impaired cerebrovascular reactivity.

---

## CPPOpt — Optimal Cerebral Perfusion Pressure

Identifies the CPP (= ABP - ICP) value where cerebral autoregulation is best preserved (PRx is minimized). Uses a binned polynomial fitting approach over a 4-hour sliding window.

### Single Window

```python
import pandas as pd
from datetime import timedelta
from cns_utils import CNSDataSource, read_time_series_data
from moberg_dsp.analytics.cppopt import cppopt_single_window
from moberg_dsp.analytics.optimal_value import OptimalValueArgs

ds = CNSDataSource("/data/patients/Patient_001")
start_us = ds.start_time()
end_us = ds.end_time()

abp = read_time_series_data("ABP,Mean,Numeric,Float,IntelliVue", ds, False, start_us, end_us)
icp = read_time_series_data("ICP,Mean,Numeric,Float,CNSMonitor", ds, False, start_us, end_us)

args = OptimalValueArgs(
    missing_data_limit_samples=120,
    num_bins=16,
    min_bin=40,                             # CPP range lower bound (mmHg)
    max_bin=120,                            # CPP range upper bound (mmHg)
    minimum_bin_percent=2,
    minimum_y_span=0.2,
    y_overlap_region_min=-0.3,
    y_overlap_region_max=0.6,
    minimum_included_data_percentage=50,
    limits_y=0.25,                          # PRx threshold
    window_size_samples=240                 # 4 hours at 1 sample/min
)

calc_start = pd.Timestamp(start_us, unit="us")
cppopt_df, cpp_5min, prx_5min = cppopt_single_window(abp, icp, args, calc_start)

# cppopt_df: DataFrame with columns [OptimalCPP, LowerLimit, UpperLimit, ...]
# cpp_5min: Series of 5-minute mean CPP values
# prx_5min: Series of 5-minute PRx values
```

### Multi-Window (Flex)

More robust — uses growing windows (2-8 hours) with R²-weighted averaging.

```python
from moberg_dsp.analytics.cppopt import cppopt_multi_window
from moberg_dsp.analytics.optimal_value_flex import OptimalValueFlexArgs

flex_args = OptimalValueFlexArgs(
    missing_data_limit_samples=120,
    num_bins=16,
    min_bin=40,
    max_bin=120,
    minimum_bin_percent=2,
    minimum_y_span=0.2,
    y_overlap_region_min=-0.3,
    y_overlap_region_max=0.6,
    minimum_included_data_percentage=50,
    limits_y=0.25,
    window_size_samples=480,                # Max 8 hours
    minimum_r_squared=0.2,
    min_window_samples=120,                 # Min 2 hours
    window_grow_samples=10,                 # Grow by 10-minute increments
    non_parabolic_window_weight=0.1
)

cppopt_df, cpp_5min, prx_5min = cppopt_multi_window(abp, icp, flex_args, calc_start)
```

**Fit Types:**
- `PARABOLA (0)`: U-shaped curve — optimal CPP is at the minimum. Most clinically useful.
- `RISING (1)`: PRx increases with CPP — suggests autoregulation may be preserved at lower CPP.
- `FALLING (-1)`: PRx decreases with CPP — suggests higher CPP is beneficial.
- `FAILED (2)`: Insufficient data or poor fit quality.

---

## MAPOpt — Optimal Mean Arterial Pressure

Identifies the MAP where cerebral oxygenation reactivity is best (COx is minimized). Same algorithm as CPPOpt but applied to MAP vs COx instead of CPP vs PRx.

### Single Window

```python
import pandas as pd
from cns_utils import CNSDataSource, read_time_series_data
from moberg_dsp.analytics.mapopt import mapopt_single_window
from moberg_dsp.analytics.optimal_value import OptimalValueArgs

ds = CNSDataSource("/data/patients/Patient_001")
start_us, end_us = ds.start_time(), ds.end_time()

abp = read_time_series_data("ABP,Mean,Numeric,Float,IntelliVue", ds, False, start_us, end_us)
sto2_left = read_time_series_data("rSO2,Left,Numeric,Float,INVOS", ds, False, start_us, end_us)
sto2_right = read_time_series_data("rSO2,Right,Numeric,Float,INVOS", ds, False, start_us, end_us)

# Average bilateral rSO2
rso2 = pd.DataFrame({"L": sto2_left, "R": sto2_right}).mean(axis=1, skipna=True)

args = OptimalValueArgs(
    missing_data_limit_samples=120,
    num_bins=50,                            # Finer binning than CPPOpt
    min_bin=20,                             # MAP range lower bound (mmHg)
    max_bin=120,                            # MAP range upper bound (mmHg)
    minimum_bin_percent=2,
    minimum_y_span=0.2,
    y_overlap_region_min=-0.3,
    y_overlap_region_max=0.6,
    minimum_included_data_percentage=50,
    limits_y=0.25,                          # COx threshold
    window_size_samples=240                 # 4 hours at 1 sample/min
)

calc_start = pd.Timestamp(start_us, unit="us")
mapopt_df, map_5min, cox_5min = mapopt_single_window(abp, rso2, args, calc_start)
```

### Multi-Window (Flex)

```python
from moberg_dsp.analytics.mapopt import mapopt_multi_window
from moberg_dsp.analytics.optimal_value_flex import OptimalValueFlexArgs

flex_args = OptimalValueFlexArgs(
    missing_data_limit_samples=120,
    num_bins=50,
    min_bin=20,
    max_bin=120,
    minimum_bin_percent=2,
    minimum_y_span=0.2,
    y_overlap_region_min=-0.3,
    y_overlap_region_max=0.6,
    minimum_included_data_percentage=50,
    limits_y=0.25,
    window_size_samples=480,
    minimum_r_squared=0.2,
    min_window_samples=120,
    window_grow_samples=10,
    non_parabolic_window_weight=0.1
)

mapopt_df, map_5min, cox_5min = mapopt_multi_window(abp, rso2, flex_args, calc_start)
```

**Key difference from CPPOpt:** MAPOpt uses 50 bins (vs 16) over a 20-120 mmHg MAP range (vs 40-120 mmHg CPP range).

---

## Spreading Depolarization Detection

Detects spreading depolarization (SD) events in multi-channel EEG using a pre-trained ML model. SD is a pathological wave of neuronal depolarization associated with secondary brain injury in TBI, stroke, and subarachnoid hemorrhage patients.

```python
import pandas as pd
from cns_utils import CNSDataSource, read_time_series_data
from moberg_dsp.sd_module import run_sd_detection

ds = CNSDataSource("/data/patients/Patient_001")
start_us, end_us = ds.start_time(), ds.end_time()

# Read raw EEG (composite, multi-channel)
raw_eeg = read_time_series_data(
    "EEG,Composite,SampleSeries,CompositeFloat,CNSMonitor",
    ds, False, start_us, end_us
)
# raw_eeg: DataFrame, columns are electrode names (Fp1, F7, T3, ...), 256 Hz typical

# Build bipolar montage (differential channels)
bipolar_pairs = [
    ("Fp1", "F7"), ("F7", "T3"), ("T3", "T5"), ("T5", "O1"),
    ("Fp2", "F8"), ("F8", "T4"), ("T4", "T6"), ("T6", "O2"),
]
bipolar = pd.DataFrame({
    f"{a}-{b}": raw_eeg[a] - raw_eeg[b]
    for a, b in bipolar_pairs
})

# Invert polarity (convention normalization)
bipolar = -bipolar

sd_events = run_sd_detection(
    bipolar,
    sampling_frequency_hz=256.0,
    feature_extraction_output_directory="/tmp/sd_output",
    detection_threshold=0.2,               # Probability threshold [0, 1]
    minimum_event_duration_seconds=10,
    cooldown_period_seconds=60,            # Min gap between events
    use_parallel=True
)
# sd_events: Series with DatetimeIndex marking event times, values = channel index
# Empty Series if no SDs detected
```

**Requirements:**
- Minimum 10 minutes of EEG data
- Input must be bipolar montage (subtract pairs of electrodes)
- Standard 10-20 system electrode naming expected
- Typical sampling rate: 256 Hz

**Parameters:**
- `detection_threshold`: Lower = more sensitive, more false positives. Default 0.2 is a reasonable starting point.
- `cooldown_period_seconds`: Suppresses duplicate detections within this window after an event.
- `minimum_event_duration_seconds`: Events shorter than this are discarded.
