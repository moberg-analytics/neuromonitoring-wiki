# moberg-dsp API Reference

Signal processing library for neurocritical care monitoring. Built on numpy, pandas, scipy, scikit-learn.

---

## moberg_dsp.analytics

### PRx — Pressure Reactivity Index

```python
from moberg_dsp.analytics.PRx import prx

prx(
    abp: pd.Series,
    icp: pd.Series,
    resample_freq: timedelta = timedelta(seconds=10),
    window_size: timedelta = timedelta(minutes=5),
    step_size: timedelta = timedelta(minutes=1)
) -> pd.Series
```

Rolling Pearson correlation between ABP and ICP. Returns Series with DatetimeIndex.

### COx — Cerebral Oximetry Index

```python
from moberg_dsp.analytics.COx import cox

cox(
    abp: pd.Series,
    stO2_left: pd.Series,
    stO2_right: pd.Series,
    resample_freq: timedelta = timedelta(seconds=10),
    window_size: timedelta = timedelta(minutes=5),
    step_size: timedelta = timedelta(minutes=1)
) -> pd.Series
```

Rolling correlation between ABP and averaged bilateral rSO2. Returns Series with DatetimeIndex.

### Optimal Value

```python
from moberg_dsp.analytics.optimal_value import (
    optimal_value, optimal_value_aligned,
    OptimalValueArgs, OptimalValueResult, OptimalValueFitResult, OptimalValueFitType
)

optimal_value(
    time_series_1: pd.Series,   # X-axis variable (e.g., CPP or MAP)
    time_series_2: pd.Series,   # Y-axis variable (e.g., PRx or COx)
    args: OptimalValueArgs
) -> OptimalValueResult

optimal_value_aligned(
    x: np.ndarray,
    y: np.ndarray,
    args: OptimalValueArgs
) -> OptimalValueResult
```

Bins X values, computes mean Y per bin, fits polynomial to find the X that minimizes Y.

### Optimal Value Flex (Multi-Window)

```python
from moberg_dsp.analytics.optimal_value_flex import (
    optimal_value_flex, optimal_value_flex_aligned,
    OptimalValueFlexArgs, OptimalValueFlexResult, OptimalValueFlexFitResult
)

optimal_value_flex(
    time_series_1: pd.Series,
    time_series_2: pd.Series,
    args: OptimalValueFlexArgs,
    start_time: pd.Timestamp,
    end_time: pd.Timestamp
) -> OptimalValueFlexResult

optimal_value_flex_aligned(
    aligned_data: pd.DataFrame,   # 2-column DataFrame [X, Y]
    args: OptimalValueFlexArgs,
    start_time: pd.Timestamp,
    end_time: pd.Timestamp
) -> OptimalValueFlexResult
```

Growing window approach with R²-weighted averaging across window sizes.

### CPPOpt — Optimal Cerebral Perfusion Pressure

```python
from moberg_dsp.analytics.cppopt import cppopt_single_window, cppopt_multi_window

cppopt_single_window(
    abp_data: pd.Series,
    icp_data: pd.Series,
    args: OptimalValueArgs,
    calculation_start_time: pd.Timestamp
) -> tuple[pd.DataFrame, pd.Series, pd.Series]
# Returns: (cppopt_results, cpp_5min, prx_5min)

cppopt_multi_window(
    abp_data: pd.Series,
    icp_data: pd.Series,
    args: OptimalValueFlexArgs,
    calculation_start_time: pd.Timestamp
) -> tuple[pd.DataFrame, pd.Series, pd.Series]
# Returns: (cppopt_results, cpp_5min, prx_5min)
```

### MAPOpt — Optimal Mean Arterial Pressure

```python
from moberg_dsp.analytics.mapopt import mapopt_single_window, mapopt_multi_window

mapopt_single_window(
    abp_data: pd.Series,
    rsO2_data: pd.Series,
    args: OptimalValueArgs,
    calculation_start_time: pd.Timestamp
) -> tuple[pd.DataFrame, pd.Series, pd.Series]
# Returns: (mapopt_results, map_5min, cox_5min)

mapopt_multi_window(
    abp_data: pd.Series,
    rsO2_data: pd.Series,
    args: OptimalValueFlexArgs,
    calculation_start_time: pd.Timestamp
) -> tuple[pd.DataFrame, pd.Series, pd.Series]
# Returns: (mapopt_results, map_5min, cox_5min)
```

### ICMP Compatibility Helpers

```python
from moberg_dsp.analytics.icmp_compatability import (
    icmp_rolling_calculation_two_variables,
    icmp_rolling_calculation_single_variable,
    TwoVariableFunction, SingleVariableFunction
)

icmp_rolling_calculation_two_variables(
    time_series_1: pd.Series,
    time_series_2: pd.Series,
    resample_freq: timedelta = timedelta(seconds=10),
    window_size: timedelta = timedelta(minutes=5),
    step_size: timedelta = timedelta(minutes=1),
    origin: pd.Timestamp = pd.Timestamp(0),
    function: TwoVariableFunction = TwoVariableFunction.CORRELATION,
    time_adjust: bool = True
) -> pd.Series

icmp_rolling_calculation_single_variable(
    time_series: pd.Series,
    resample_freq: timedelta = timedelta(seconds=10),
    window_size: timedelta = timedelta(minutes=5),
    step_size: timedelta = timedelta(minutes=1),
    origin: pd.Timestamp = pd.Timestamp(0),
    function: SingleVariableFunction = SingleVariableFunction.MEAN
) -> pd.Series
```

---

## Data Structures

### OptimalValueArgs

```python
@dataclass
class OptimalValueArgs:
    missing_data_limit_samples: int       # Max allowed missing samples
    num_bins: int                         # Number of X-axis bins
    min_bin: int                          # Minimum X value (e.g., 40 mmHg for CPP)
    max_bin: int                          # Maximum X value (e.g., 120 mmHg for CPP)
    minimum_bin_percent: int              # Min % data per bin to include
    minimum_y_span: int                   # Min Y range required for valid fit
    y_overlap_region_min: int             # Y lower bound for limit detection
    y_overlap_region_max: int             # Y upper bound for limit detection
    minimum_included_data_percentage: int  # Min % non-missing data required
    limits_y: int                         # Y threshold for autoregulation limits (e.g., PRx=0.25)
    window_size_samples: int              # Analysis window in samples
```

### OptimalValueFlexArgs

Extends `OptimalValueArgs` with:
```python
@dataclass
class OptimalValueFlexArgs(OptimalValueArgs):
    minimum_r_squared: float              # Min R² for valid polynomial fit
    min_window_samples: int               # Smallest window to try
    window_grow_samples: int              # Window growth increment
    non_parabolic_window_weight: float    # Weight multiplier for non-parabolic fits
```

### OptimalValueFitType

```python
class OptimalValueFitType:
    PARABOLA = 0    # U-shaped fit (optimal value exists)
    RISING = 1      # Monotonically increasing
    FALLING = -1    # Monotonically decreasing
    FAILED = 2      # Fit did not converge
```

### OptimalValueResult

```python
@dataclass
class OptimalValueResult(OptimalValueFitResult):
    optimal_x: float                      # Optimal value found
    bin_means: np.ndarray                 # Mean Y per bin
    bin_errors: np.ndarray                # Standard error per bin
    bin_data_percentages: np.ndarray      # % data per bin
    coefficients: np.ndarray              # Polynomial coefficients [a, b, c]
    fit_type: OptimalValueFitType
    fit_span: float                       # Max - min of fitted curve
    included_data_percent: float
    bin_included_in_fit: np.ndarray       # Boolean mask
    poly_fit_x: np.ndarray               # X values used in fit
    poly_fit_y: np.ndarray               # Y values used in fit
    lla: float                            # Lower Limit of Autoregulation
    ula: float                            # Upper Limit of Autoregulation
    window_size_seconds: int

    def get_fit_type_as_number() -> float
    def to_numpy() -> np.ndarray          # Serialize to 2D array
    @staticmethod
    def from_numpy(array, num_bins) -> OptimalValueResult
    @staticmethod
    def get_numpy_labels(optimal_x_label, num_bins) -> list[str]
```

### OptimalValueFlexResult

```python
@dataclass
class OptimalValueFlexResult:
    weighted_optimal_x: float             # Weighted optimal across windows
    weighted_lla: float                   # Weighted lower limit
    weighted_ula: float                   # Weighted upper limit
    best: OptimalValueFlexFitResult       # Best single-window result

    def to_numpy() -> np.ndarray
    @staticmethod
    def from_numpy(array, num_bins) -> OptimalValueFlexResult
    @staticmethod
    def get_numpy_labels(optimal_x_label, num_bins) -> list[str]
```

`OptimalValueFlexFitResult` extends `OptimalValueResult` with `weight: float` (R² value).

---

## moberg_dsp.filtering

### EEG Filters

```python
from moberg_dsp.filtering.EEG import (
    get_eeg_filters,
    get_eeg_filter_settling_time_samples
)

get_eeg_filters(
    LFF: float | None = None,    # Low-frequency filter cutoff (Hz) — attenuates below
    HFF: float | None = None,    # High-frequency filter cutoff (Hz) — attenuates above
    notch: float | None = None   # Notch filter frequency (Hz)
) -> np.ndarray | None
```

Returns Butterworth IIR filters as Second Order Sections (SOS) matrix. Cached. Returns None if no filters specified.

Supported values: HFF: 0.5-100 Hz, LFF: 0.005-3 Hz, notch: 50/60 Hz.

```python
get_eeg_filter_settling_time_samples(
    LFF: float | None = None,
    HFF: float | None = None,
    notch: float | None = None
) -> int
```

Samples needed to prime filters before valid output begins.

```python
from moberg_dsp.filtering.units import hz_to_time_constant

hz_to_time_constant(hz: float) -> float  # Returns RC time constant: 1/(2*pi*fc)
```

---

## moberg_dsp.preprocessing

```python
from moberg_dsp.preprocessing import (
    align_time_series,
    resample,
    forward_fill,
    ensure_fixed_sample_rate,
    get_next_aligned_timestamp
)
from moberg_dsp.preprocessing.transform import fisher, inverse_fisher
```

### align_time_series

```python
align_time_series(
    time_series_1: pd.Series | pd.DataFrame,
    time_series_2: pd.Series | pd.DataFrame
) -> tuple[pd.Series | pd.DataFrame, pd.Series | pd.DataFrame]
```

Aligns two time series to a common fixed-frequency grid using nearest-neighbor resampling. Required before cross-signal operations.

### resample

```python
resample(
    data: pd.Series | pd.DataFrame,
    resample_duration_ms: int,
    min_timestamp: pd.Timestamp,
    max_timestamp: pd.Timestamp
) -> pd.Series | pd.DataFrame
```

Resamples to fixed grid aligned to epoch origin, clipped to the specified time range.

### forward_fill

```python
forward_fill(data: np.ndarray) -> np.ndarray
```

Fills NaN values with last known value. In-place.

### ensure_fixed_sample_rate

```python
ensure_fixed_sample_rate(index: pd.DatetimeIndex) -> None
```

Validates/infers frequency attribute on DatetimeIndex. Raises AssertionError if not evenly spaced.

### fisher / inverse_fisher

```python
fisher(data: np.ndarray) -> np.ndarray          # arctanh(data)
inverse_fisher(data: np.ndarray) -> np.ndarray   # tanh(data)
```

Fisher transform for correlation coefficients. Reduces clamping near -1/+1 bounds.

### get_next_aligned_timestamp

```python
get_next_aligned_timestamp(timestamp_us: int, calculation_period_us: int) -> int
```

Snaps a microsecond timestamp to the next grid boundary.

---

## moberg_dsp.statistics

```python
from moberg_dsp.statistics import (
    rolling_average,
    weighted_average,
    exponentially_weighted_moving_average,
    binned_mean_and_standard_error,
    r_squared
)
```

### rolling_average

```python
rolling_average(
    time_series: pd.Series | pd.DataFrame,
    window: int,    # Window size in samples
    step: int,      # Step size in samples
    offset: int = 0
) -> pd.Series | pd.DataFrame
```

Sample-based rolling mean. Returns same type as input.

### weighted_average

```python
weighted_average(values: np.ndarray, weights: np.ndarray) -> float
```

Normalized weighted average. Filters NaN values. Returns NaN if empty.

### exponentially_weighted_moving_average

```python
exponentially_weighted_moving_average(
    data: np.ndarray,
    alpha: float,     # Smoothing factor in [0, 1] — higher = more responsive
    axis: int = -1
) -> np.ndarray
```

IIR-based EWMA: `y[i] = alpha * x[i] + (1 - alpha) * y[i-1]`. Forward-fills NaN before filtering.

### binned_mean_and_standard_error

```python
binned_mean_and_standard_error(
    y: np.ndarray,
    num_bins: int,
    bin_indexes: np.ndarray,
    minimum_bin_percentage: float
) -> tuple[np.ndarray, np.ndarray, np.ndarray, np.ndarray]
# Returns: (bin_means, standard_errors, counts, excluded_bins)
```

### r_squared

```python
r_squared(x: np.ndarray, y: np.ndarray, coefficients: np.ndarray) -> float
```

R² for polynomial fit. Coefficients in descending degree order.

---

## moberg_dsp.epoching

```python
from moberg_dsp.epoching import (
    rolling_windows,
    rolling_window_frames,
    rolling_window_frames_by_time,
    get_rolling_window_offset,
    growing_windows,
    growing_window_frames,
    process_data_in_chunks
)
```

### rolling_windows

```python
rolling_windows(
    data_frame: pd.DataFrame | pd.Series,
    window: int,         # Window size in samples
    step: int,           # Step size in samples
    offset: int = 0,
    origin_timestamp_microseconds: int | None = None
) -> tuple[np.ndarray, pd.DatetimeIndex]
```

Returns strided array views for vectorized windowed operations. Very efficient for large datasets.

### rolling_window_frames

```python
rolling_window_frames(
    data_frame: pd.DataFrame,
    window_size: int,
    step_size: int,
    skip_partial_windows: bool = False
) -> Generator[pd.DataFrame, None, None]
```

Generator yielding DataFrame windows. Includes growing phase from 1 sample up to `window_size`.

### rolling_window_frames_by_time

```python
rolling_window_frames_by_time(
    data_frame: pd.DataFrame,
    first_window_end_time: pd.Timestamp,
    window_size: timedelta,
    step_size: timedelta,
    minimum_window_length: int = 0
) -> Generator[tuple[pd.DataFrame, pd.Timestamp, pd.Timestamp], None, None]
```

Time-based rolling windows. Yields `(window_data, start_time, end_time)`. Requires DatetimeIndex.

### growing_windows

```python
growing_windows(
    data_frame: pd.DataFrame | pd.Series,
    initial_window: int,    # Starting window size in samples
    grow_step: int,         # Growth increment in samples
    reverse: bool = False   # True: grow backward from end
) -> Generator[pd.DataFrame | pd.Series, None, None]
```

### growing_window_frames

```python
growing_window_frames(
    data_frame: pd.DataFrame | pd.Series,
    end_time: pd.Timestamp,
    minimum_window_size: timedelta,
    maximum_window_size: timedelta,
    step_size: timedelta
) -> Generator[tuple[pd.DataFrame | pd.Series, pd.Timestamp, pd.Timestamp], None, None]
```

Time-based growing windows. Yields `(window_data, start_time, end_time)`.

### process_data_in_chunks

```python
process_data_in_chunks(
    data_loader: Callable[[pd.Timestamp, pd.Timestamp, list[str] | None], pd.DataFrame],
    data_processor: Callable[[pd.DataFrame, Callable, pd.Timestamp, pd.Timestamp], Any],
    start_time: pd.Timestamp,
    end_time: pd.Timestamp,
    chunk_size: timedelta,
    chunk_pre_read: timedelta | None = timedelta(seconds=0),
    logs: list[str] | None = None   # ["status", "performance", "progress"]
) -> Iterator[Any]
```

Processes large time ranges in memory-efficient chunks. `data_loader` fetches data for a time range. `data_processor` receives data + a clamp function to trim to chunk boundaries.

---

## moberg_dsp.sd_module

### Spreading Depolarization Detection

```python
from moberg_dsp.sd_module import run_sd_detection

run_sd_detection(
    input_data: pd.DataFrame,              # EEG data, DatetimeIndex, columns=channels
    sampling_frequency_hz: float,          # Typically 256 Hz
    feature_extraction_output_directory: str,
    feature_extraction_hz: float = 0.1,
    decimation_filter_samples: int = 256,
    feature_extraction_window_size: int = 400,
    detection_threshold: float = 0.2,      # Probability threshold [0, 1]
    minimum_event_duration_seconds: float = 10,
    cooldown_period_seconds: float = 60,   # Min time between events
    use_parallel: bool = True,
    verbose: bool = False
) -> pd.Series
```

ML-based detection using pre-trained XGBoost model. Input must be bipolar EEG montage (differential channels). Requires minimum 10 minutes of data. Returns Series with DatetimeIndex — values indicate channel index of detected events. Empty Series if no events found.
