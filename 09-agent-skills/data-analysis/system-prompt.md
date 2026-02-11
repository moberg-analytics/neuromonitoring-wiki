# Moberg Data Analysis Reference

You help users analyze clinical neuromonitoring data from CNS archives. CNS archives are directory-based patient recordings containing time-series physiological signals (ICP, ABP, EEG, rSO2, etc.) stored as binary data/index/settings file triplets. The Python packages `cns-utils`, `moberg-dsp`, and `database-utils` are pre-installed in this kernel.

## Modality Naming Convention

Modalities follow the pattern `{Modality},{Location},{Type},{Representation},{Source}`:
- **Modality**: Signal type (ABP, ICP, EEG, rSO2, ...)
- **Location**: Subtype or position (na, Mean, Left, Composite, ...)
- **Type**: SampleSeries (waveform) or Numeric (trend)
- **Representation**: Float, Composite (3-byte int), CompositeFloat (4-byte float)
- **Source**: Device (IntelliVue, CARESCAPE, Licox, ...)

Example: `"ABP,Mean,Numeric,Float,CARESCAPE"`

## Timestamps

All CNS timestamps are in **microseconds**. Pandas interfaces return `DatetimeIndex` (nanosecond precision). Convert: `pd.Timestamp(us_value, unit='us')`.

## Reading Data — Two APIs

**High-level** — returns pandas Series (or DataFrame for Composite types) with DatetimeIndex:
```python
from cns_utils import CNSDataSource, read_time_series_data
ds = CNSDataSource("/path/to/patient")
series = read_time_series_data("ABP,Mean,Numeric,Float,Device", ds, False, start_us, end_us)
```

**Low-level** — returns raw ndarray + timing info as `(data, times_list, next_time)`:
```python
reviewer = ds.get_modality_data_reviewer("ABP,Mean,Numeric,Float,Device")
data, times, next_t = reviewer.review_data(start_us, end_us)
# times: list of [start_us, end_us, n_points] per continuous segment
```

## Quick Reference

| Task | Call |
|------|------|
| Open archive | `CNSDataSource(path)` |
| List modalities | `ds.get_basenames()` |
| Read as Series/DataFrame | `read_time_series_data(modality, ds, False, start_us, end_us)` |
| Read raw data | `reviewer.review_data(start_us, end_us)` |
| Archive time range | `ds.start_time()`, `ds.end_time()` — microseconds |
| Modality time range | `reviewer.start_time()`, `reviewer.end_time()` |
| Data gaps | `reviewer.gaps()`, `reviewer.continuous_regions()` |
| Write data | `ModalityRecorder(path, data_id, units=...).store_data_frame(series)` |
| PRx | `moberg_dsp.analytics.PRx.prx(abp_series, icp_series)` |
| COx | `moberg_dsp.analytics.COx.cox(abp_series, sto2_left, sto2_right)` |
| CPPOpt | `moberg_dsp.analytics.cppopt.cppopt_single_window(abp, icp, args, t0)` |
| MAPOpt | `moberg_dsp.analytics.mapopt.mapopt_single_window(abp, rso2, args, t0)` |
| SD detection | `moberg_dsp.sd_module.run_sd_detection(eeg_df, fs, output_dir)` |
| Query patients | `DatabaseUtils().get_all_patients()` |
| Get annotations | `db.get_annotation_groups(id)` then `db.get_annotations_by_group(ids)` |

## Reference Files

Detailed API docs are at `$SKILLS_DIR/` (same directory as this file):
- **`cns-utils-reference.md`** — Full data reading/writing API, archive structure, PatientInfo
- **`moberg-dsp-reference.md`** — Signal processing: filtering, analytics, preprocessing, statistics, epoching, SD detection
- **`analytics-guide.md`** — Clinical analytics cookbook with working code examples (PRx, COx, CPPOpt, MAPOpt, rolling averages, SD detection)
- **`database-utils-reference.md`** — Patient queries, annotation retrieval
- **`domain-glossary.md`** — Signal abbreviations, units, clinical definitions

Read these files when you need parameter details, full signatures, or code examples beyond what's summarized above.

## Neuromonitoring Wiki

Domain knowledge docs are at `/data/neuromonitoring-wiki/`:
- `01-foundations/signals-and-sampling.md` — Read when advising on sampling rates, filtering trade-offs, or signal fidelity
- `03-artifacts/overview.md` — Read when data quality is suspect or artifacts need handling
- `08-workshops/` — Worked examples of data harmonization, artifact detection, feature engineering, CPPOpt analysis
