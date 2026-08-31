# cns-utils API Reference

## CNSDataSource

Main entry point for reading CNS archives.

```python
from cns_utils import CNSDataSource

ds = CNSDataSource(cnsdir: str, relevant_basenames: list[str] | None = None)
```

- `cnsdir`: Path to patient archive directory
- `relevant_basenames`: Optional filter — only index these modalities (speeds up init)

Auto-detects segmented vs non-segmented archives.

### Methods

```python
ds.get_basenames() -> list[str]
```
Returns all supported modality basenames in the archive.

```python
ds.get_modality_data_reviewer(data_id: str) -> ModalityReviewer
```
Returns a reviewer for the given modality. `data_id` is the full basename string (e.g., `"ABP,na,SampleSeries,Float,IntelliVue"`).

```python
ds.start_time() -> int   # microseconds
ds.end_time() -> int     # microseconds
```
Archive-wide time boundaries.

```python
ds.get_measurements() -> Iterable[dict]
```
Metadata for all measurements (modality name, time range, format info).

```python
ds.get_data_format(data_id: str) -> CNSTimeSeriesFormat | None
```
Format descriptor for a modality (sample size, representation type).

```python
ds.get_patient_info(patient_info_path: str) -> PatientInfo
```
Parse patient.info XML. See PatientInfo section below.

```python
ds.get_eeg_montage_mapping() -> EEGMontageMapping
```
EEG channel mappings from Montage.xml (if present).

```python
ds.basename_is_supported(basename: str) -> bool
```
Check if a modality can be read.

```python
ds.shift(date_offset: timedelta) -> None
```
Shift all data timestamps in-place.

### Factory Function

```python
from cns_utils import get_data_source
ds = get_data_source(cns_dir: str) -> CNSDataSource
```
Cached — returns the same instance for repeated calls with the same path.

---

## read_time_series_data

High-level convenience function for reading data as pandas objects.

```python
from cns_utils import read_time_series_data

result = read_time_series_data(
    modality: str,                    # Full basename string
    patient_data_source: CNSDataSource,
    patient_is_admitted: bool,        # True refreshes data for live recordings
    start_time_us: int,               # Microseconds
    end_time_us: int                  # Microseconds
) -> pd.Series | pd.DataFrame
```

**Returns:**
- `pd.Series` for single-channel data (Float representation) — index is DatetimeIndex, values are float32
- `pd.DataFrame` for Composite/CompositeFloat data — index is DatetimeIndex, columns are channel names

This is a wrapper around `ModalityReviewer.review_data_frame()`.

---

## ModalityReviewer

Reads data for a single modality across all segments.

```python
reviewer = ds.get_modality_data_reviewer(data_id)
```

### Reading Data

#### review_data_frame (high-level)

```python
reviewer.review_data_frame(
    start_time: float,   # Microseconds
    end_time: float      # Microseconds
) -> tuple[pd.Series | pd.DataFrame, float | None]
```

Returns `(data, next_time)`:
- `data`: Series (1D) or DataFrame (2D composite) with DatetimeIndex
- `next_time`: Microsecond timestamp of next available data point, or None at end

Raises `CNSDataIntegrityError` if timestamps are non-monotonic.

#### review_data (low-level)

```python
reviewer.review_data(
    start_time: float,   # Microseconds
    end_time: float      # Microseconds (inclusive)
) -> tuple[np.ndarray, list, float]
```

Returns `(data, times, next_time)`:
- `data`: ndarray — 1D `(n_samples,)` for single-channel, 2D `(n_samples, n_channels)` for composite
- `times`: List of `[start_us, end_us, n_points]` per continuous segment (multiple entries if gaps exist)
- `next_time`: Next data timestamp or None

### Comparison

| | `review_data_frame()` | `review_data()` |
|---|---|---|
| Returns | Series/DataFrame with DatetimeIndex | ndarray + separate timing list |
| Gaps | Handled automatically | Explicit in times list |
| Channel names | From settings file | Not included |
| Use case | Analysis-ready pandas workflow | Custom low-level processing |

### Metadata Methods

```python
reviewer.start_time() -> float       # Modality start (microseconds)
reviewer.end_time() -> float         # Modality end (microseconds)
reviewer.total_samples() -> int
reviewer.gaps() -> list[tuple]       # Time gaps in recording
reviewer.continuous_regions() -> list[tuple]  # Continuous time intervals
reviewer.settings() -> dict          # Units, channels, conversion factors
reviewer.settings_element_tree() -> ElementTree  # Raw XML settings
reviewer.refresh()                   # Reload for live recordings
```

---

## ModalityRecorder

Writes data to CNS archives.

```python
from cns_utils import ModalityRecorder

writer = ModalityRecorder(
    cnsdir: str,
    data_id: str,                            # e.g., "BP,Systolic,SampleSeries,Float,MyTool"
    units: str | None = None,                # e.g., "mmHg"
    composite_columns: list[str] | None = None,  # Column names for multi-channel
    write_mode: str = "w"                    # "w" (new) or "a" (append)
)
```

### Writing Data

```python
writer.store_data_frame(data: pd.Series | pd.DataFrame) -> None
```

- **Series input**: Must be float32, datetime-indexed
- **DataFrame input**: All columns float32, datetime-indexed (for composite data)
- Automatically handles gap detection and index file generation

---

## PatientInfo

Dataclass from patient.info XML.

```python
from cns_utils.patient_info import PatientInfo

info = PatientInfo.from_xml(xml_path: str | Path) -> PatientInfo
```

**Fields:**
- `FileVersion: float`
- `SoftwareVersion: str`
- `SystemName: str`
- `PatientFirstName: str`, `PatientLastName: str`
- `MedicalRecordNumber: str`
- `PhysicianName: str`
- `Gender: str`
- `Birthdate: str`
- `AdmissionTime: int` (microseconds)
- `DischargeTime: int` (microseconds)
- `Timezone: str`
- `Protocol: str`
- `RecordingEndTime: int`, `SegmentEndTime: int`
- `AdmissionSystemOffset: int`, `SystemOffset: int`

```python
info.write(directory: str) -> None  # Write back to XML
```

---

## harmonize

Merges multiple modalities into a single time series with priority ordering.

```python
from cns_utils import harmonize

harmonize(
    source_cns_path: str,
    destination_directory_path: str,
    harmonization_map: dict[str, list[str]],  # output_modality -> [source_modalities] (priority order)
    chunk_size: timedelta = timedelta(hours=1)
) -> None
```

Example:
```python
harmonize("/data/patient", "/data/harmonized", {
    "ABP,Mean,Numeric,Float,Harmonized": [
        "ABP,Mean,Numeric,Float,IntelliVue",
        "ABP,Mean,Numeric,Float,CARESCAPE",
    ]
})
```

---

## Archive Structure

### Non-segmented
```
Patient_YYYY_MMM_D__HH_MM_SS_XXXXXX/
  patient.info                           # XML metadata
  Montage.xml                            # EEG channel map (optional)
  ABP,na,SampleSeries,Float,Device,data      # Binary signal data
  ABP,na,SampleSeries,Float,Device,index     # Timing index (28 bytes/record)
  ABP,na,SampleSeries,Float,Device,settings  # XML metadata (units, etc.)
  ICP,na,SampleSeries,Float,Device,{data,index,settings}
  EEG,Composite,SampleSeries,CompositeFloat,Device,{data,index,settings}
  ...
```

### Segmented
```
Patient_YYYY_MMM_D__HH_MM_SS_XXXXXX/
  admission.info
  Segment_001/
    patient.info
    Montage.xml
    {modality files}
  Segment_002/
    patient.info
    {modality files}
```

### File Triplet

Each modality has three files:
- **`.data`**: Raw binary samples (float32 for Float, 3-byte packed ints for Composite, etc.)
- **`.index`**: 28-byte records mapping file positions to timestamps with sample intervals
- **`.settings`**: XML with units, composite channel names, conversion factors

### Modality Naming

`{Modality},{Location},{Type},{Representation},{Source}`

| Aspect | Common Values |
|--------|---------------|
| Modality | ABP, ICP, EEG, rSO2, HR, SpO2, Temp, PbtO2 |
| Location | na, Mean, Systolic, Diastolic, Left, Right, Composite |
| Type | SampleSeries (waveform, ~125-256 Hz), Numeric (trend, ~0.2 Hz) |
| Representation | Float (4-byte), Composite (3-byte int, multi-channel), CompositeFloat (4-byte float, multi-channel) |
| Source | IntelliVue, CARESCAPE, Licox, INVOS, CNSMonitor, ... |

### Composite Data

Multi-channel signals where multiple values share each timestamp:
- EEG (19+ channels, typically Composite representation at 256 Hz)
- Multi-parameter trends (e.g., bilateral rSO2)

Composite data returns `DataFrame` from `review_data_frame()` and 2D ndarray from `review_data()`. Channel names come from the settings file's `<CompositeElements>` XML.

---

## Errors

```python
from cns_utils.errors import (
    CNSNotFoundError,           # Resource not found
    CNSWriteError,              # Write operation failed
    CNSReadError,               # Read operation failed
    CNSStructureIntegrityError, # File structure/naming issues
    CNSDataIntegrityError,      # Data validation issues (e.g., non-monotonic timestamps)
)
```

---

## Examples

### List available modalities and time range
```python
from cns_utils import CNSDataSource

ds = CNSDataSource("/data/patients/Patient_2024_Jan_15__08_30_00_000000")
for basename in ds.get_basenames():
    reviewer = ds.get_modality_data_reviewer(basename)
    print(f"{basename}: {reviewer.total_samples()} samples")
```

### Read a single-channel signal
```python
from cns_utils import CNSDataSource, read_time_series_data

ds = CNSDataSource("/data/patients/Patient_2024_Jan_15__08_30_00_000000")
icp = read_time_series_data(
    "ICP,Mean,Numeric,Float,CNSMonitor",
    ds, False,
    ds.start_time(),
    ds.end_time()
)
# icp: pd.Series, index=DatetimeIndex, values=float32
```

### Read composite EEG data
```python
eeg = read_time_series_data(
    "EEG,Composite,SampleSeries,CompositeFloat,CNSMonitor",
    ds, False,
    ds.start_time(),
    ds.start_time() + 60_000_000  # 60 seconds
)
# eeg: pd.DataFrame, columns=channel names, index=DatetimeIndex
```

### Write processed data back
```python
from cns_utils import ModalityRecorder
import numpy as np

writer = ModalityRecorder(
    "/data/output",
    "ICP,Mean,SampleSeries,Float,Processed",
    units="mmHg"
)
writer.store_data_frame(icp.astype(np.float32))
```
