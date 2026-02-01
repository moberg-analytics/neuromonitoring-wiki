# Advanced Neuromonitoring in the Age of AI <br> Neuroinformatics and AI in the NeuroICU
2025 International Conference on Spreading Depolarizations (ICSD) \
December 8, 2025 in Old and Royal Windsor, UK.

## Notebook 1 - Data Handling

Learn how to load, explore, and validate high-frequency physiologic data using pycns. You will navigate patient records, examine synchronized multi-signal time series (HR, ABP, ICP, ECG, respiratory signals) captured in the patient archive. This section introduces the foundational tools needed to work with large-scale neurocritical care datasets.

**For more information or help, please email support@moberganalytics.com**

What you'll learn:
* How different physiologic data types are organized in the Moberg Clinical Platform (trends vs waveforms, metadata, timestamps).
* How to load high-frequency physiological data (EEG, ICP, ABP, ECG, respiratory signals) from patient archives using pycns.
* How to navigate, inspect, and validate patient recordings to ensure signals are aligned, complete, and ready for analysis.
* Why proper data handling is essential for downstream tasks like event detection, feature extraction, and AI modelling.

### Exercise 0. Finding TRACK-TBI Data

**Goal: Understand where the data lives and how it is organized.**

Patient archives on MCP are stored in a folder system, much like the folders and files you use every day on your own computer.

At the very top, we have one main folder that holds everything:

`/mnt/s3/tracktbi-data-main`

Inside this folder, the first thing you see are patient identifiers.
* Each patient has a unique identifier, called a patient ID (e.g.,, 1, 2).
* This IDs ensures that each patient’s data is kept separate and distinct from every other patient.


Under each patient’s folder, the data is divided into subfolders by type:
* cns/ → Natus CNS monitor archives
* edf/ → EEG and physiologic waveform files in EDF format
* medications/ → drug administration logs
* …and other folders depending on what was collected.

So the structure looks like this:

```{bash}
/mnt/s3/tracktbi-data-main
    ├── 1
    │     ├── cns/
    │     ├── edf/
    │     └── medications/
    ├── 2
    │     ├── cns/
    │     └── edf/
    └── 3
          └── cns/
```

Each data type contains its corresponding data files for a patient.

Key idea: The hierarchy reflects concepts we care about:
* Patient ID → uniquely identifies the patient.
* Data type (cns, edf, medications, etc.) → groups together all files of the same category.
* Files themselves → the raw data.

This organization allows us to quickly find data for any patient and ensures that large numbers of files can be managed consistently across the database.

Example path:

`/mnt/s3/tracktbi-data-main/1/cns/Patient_2014_Dec_16__12_48_34_830639`

This path corresponds to a Natus CNS Monitor archive called `Patient_2014_Dec_16__12_48_34_830639` for patient ID `1`.

This organization allows us to quickly find the raw files for each patient.

Execute the commands below to explore the TRACK-TBI database. Feel free to make changes to the following commands to explore the database. For example, you can change `1` in `!ls /mnt/s3/tracktbi-data-main/1` to `10` to view the contents of a different patient. 


```python
!ls /mnt/s3/tracktbi-data-main
```

    1    106  113  120  129  15   162  22  3   38  45  53  60  69  78  86  93
    10   107  114  121  13	 153  163  23  30  39  46  54  61  7   8   87  94
    100  108  115  123  130  156  166  24  32  4   47  55  62  70  80  88  95
    101  109  116  124  131  158  167  25  33  40  48  56  64  71  81  89  96
    102  11   117  125  132  159  17   26  34  41  5   57  65  72  82  9   97
    103  110  118  126  133  16   18   27  35  42  50  58  66  73  83  90  98
    104  111  119  127  134  160  19   28  36  43  51  59  67  75  84  91  99
    105  112  12   128  14	 161  21   29  37  44  52  6   68  76  85  92


Each patient will have a set of subfolders for the data types.


```python
!ls /mnt/s3/tracktbi-data-main/1
```

    cns


Then each data type folder has the corresponding data archives, which can be files or directories.


```python
!ls /mnt/s3/tracktbi-data-main/1/cns
```

    Patient_2014_Dec_16__12_48_34_830639


In this case, we're looking at the contents of a specific Natus CNS Archive.


```python
!ls /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_Dec_16__12_48_34_830639
```

    ABP,Dias,Numeric,Float,IntelliVue,data
    ABP,Dias,Numeric,Float,IntelliVue,index
    ABP,Dias,Numeric,Float,IntelliVue,settings
    ABP,Dias,NumericQuality,Integer,IntelliVue,data
    ABP,Dias,NumericQuality,Integer,IntelliVue,index
    ABP,Mean,Numeric,Float,IntelliVue,data
    ABP,Mean,Numeric,Float,IntelliVue,index
    ABP,Mean,Numeric,Float,IntelliVue,settings
    ABP,Mean,NumericQuality,Integer,IntelliVue,data
    ABP,Mean,NumericQuality,Integer,IntelliVue,index
    ABP,na,SampleSeries,Integer,IntelliVue,data
    ABP,na,SampleSeries,Integer,IntelliVue,index
    ABP,na,SampleSeries,Integer,IntelliVue,settings
    ABP,na,WaveQuality,Integer,IntelliVue,data
    ABP,na,WaveQuality,Integer,IntelliVue,index
    ABP,Syst,Numeric,Float,IntelliVue,data
    ABP,Syst,Numeric,Float,IntelliVue,index
    ABP,Syst,Numeric,Float,IntelliVue,settings
    ABP,Syst,NumericQuality,Integer,IntelliVue,data
    ABP,Syst,NumericQuality,Integer,IntelliVue,index
    Alert,na,Event,Char,CNS,data
    Alert,na,Event,Char,CNS,textdata
    ArchiveInventory.xml
    ART,Dias,Numeric,Float,IntelliVue,data
    ART,Dias,Numeric,Float,IntelliVue,index
    ART,Dias,Numeric,Float,IntelliVue,settings
    ART,Dias,NumericQuality,Integer,IntelliVue,data
    ART,Dias,NumericQuality,Integer,IntelliVue,index
    ART,Mean,Numeric,Float,IntelliVue,data
    ART,Mean,Numeric,Float,IntelliVue,index
    ART,Mean,Numeric,Float,IntelliVue,settings
    ART,Mean,NumericQuality,Integer,IntelliVue,data
    ART,Mean,NumericQuality,Integer,IntelliVue,index
    ART,na,SampleSeries,Integer,IntelliVue,data
    ART,na,SampleSeries,Integer,IntelliVue,index
    ART,na,SampleSeries,Integer,IntelliVue,settings
    ART,na,WaveQuality,Integer,IntelliVue,data
    ART,na,WaveQuality,Integer,IntelliVue,index
    ART,Syst,Numeric,Float,IntelliVue,data
    ART,Syst,Numeric,Float,IntelliVue,index
    ART,Syst,Numeric,Float,IntelliVue,settings
    ART,Syst,NumericQuality,Integer,IntelliVue,data
    ART,Syst,NumericQuality,Integer,IntelliVue,index
    CPP,na,Numeric,Float,IntelliVue,data
    CPP,na,Numeric,Float,IntelliVue,index
    CPP,na,Numeric,Float,IntelliVue,settings
    CPP,na,NumericQuality,Integer,IntelliVue,data
    CPP,na,NumericQuality,Integer,IntelliVue,index
    CVP,Mean,Numeric,Float,IntelliVue,data
    CVP,Mean,Numeric,Float,IntelliVue,index
    CVP,Mean,Numeric,Float,IntelliVue,settings
    CVP,Mean,NumericQuality,Integer,IntelliVue,data
    CVP,Mean,NumericQuality,Integer,IntelliVue,index
    CVP,na,SampleSeries,Integer,IntelliVue,data
    CVP,na,SampleSeries,Integer,IntelliVue,index
    CVP,na,SampleSeries,Integer,IntelliVue,settings
    CVP,na,WaveQuality,Integer,IntelliVue,data
    CVP,na,WaveQuality,Integer,IntelliVue,index
    ECG,II,SampleSeries,Integer,IntelliVue,data
    ECG,II,SampleSeries,Integer,IntelliVue,index
    ECG,II,SampleSeries,Integer,IntelliVue,settings
    ECG,II,WaveQuality,Integer,IntelliVue,data
    ECG,II,WaveQuality,Integer,IntelliVue,index
    Events.xml
    HR,na,Numeric,Float,IntelliVue,data
    HR,na,Numeric,Float,IntelliVue,index
    HR,na,Numeric,Float,IntelliVue,settings
    HR,na,NumericQuality,Integer,IntelliVue,data
    HR,na,NumericQuality,Integer,IntelliVue,index
    ICP,Mean,Numeric,Float,IntelliVue,data
    ICP,Mean,Numeric,Float,IntelliVue,index
    ICP,Mean,Numeric,Float,IntelliVue,settings
    ICP,Mean,NumericQuality,Integer,IntelliVue,data
    ICP,Mean,NumericQuality,Integer,IntelliVue,index
    ICP,na,SampleSeries,Integer,IntelliVue,data
    ICP,na,SampleSeries,Integer,IntelliVue,index
    ICP,na,SampleSeries,Integer,IntelliVue,settings
    ICP,na,WaveQuality,Integer,IntelliVue,data
    ICP,na,WaveQuality,Integer,IntelliVue,index
    ICT,na,Numeric,Float,Licox,data
    ICT,na,Numeric,Float,Licox,index
    ICT,na,Numeric,Float,Licox,settings
    ICT,na,NumericQuality,Integer,Licox,data
    ICT,na,NumericQuality,Integer,Licox,index
    MD5_from_MS_CertUtil.txt
    NBP,Dias,Numeric,Float,IntelliVue,data
    NBP,Dias,Numeric,Float,IntelliVue,index
    NBP,Dias,Numeric,Float,IntelliVue,settings
    NBP,Dias,NumericQuality,Integer,IntelliVue,data
    NBP,Dias,NumericQuality,Integer,IntelliVue,index
    NBP,Mean,Numeric,Float,IntelliVue,data
    NBP,Mean,Numeric,Float,IntelliVue,index
    NBP,Mean,Numeric,Float,IntelliVue,settings
    NBP,Mean,NumericQuality,Integer,IntelliVue,data
    NBP,Mean,NumericQuality,Integer,IntelliVue,index
    NBP,Syst,Numeric,Float,IntelliVue,data
    NBP,Syst,Numeric,Float,IntelliVue,index
    NBP,Syst,Numeric,Float,IntelliVue,settings
    NBP,Syst,NumericQuality,Integer,IntelliVue,data
    NBP,Syst,NumericQuality,Integer,IntelliVue,index
    NextEventNum
    Overrides.xml
    patient.info
    PbtO2,na,Numeric,Float,Licox,data
    PbtO2,na,Numeric,Float,Licox,index
    PbtO2,na,Numeric,Float,Licox,settings
    PbtO2,na,NumericQuality,Integer,Licox,data
    PbtO2,na,NumericQuality,Integer,Licox,index
    PLETH,na,SampleSeries,Integer,IntelliVue,data
    PLETH,na,SampleSeries,Integer,IntelliVue,index
    PLETH,na,SampleSeries,Integer,IntelliVue,settings
    PLETH,na,WaveQuality,Integer,IntelliVue,data
    PLETH,na,WaveQuality,Integer,IntelliVue,index
    Processed
    protocols
    protocolSettings.xml
    protocol.xml
    RESP,na,SampleSeries,Integer,IntelliVue,data
    RESP,na,SampleSeries,Integer,IntelliVue,index
    RESP,na,SampleSeries,Integer,IntelliVue,settings
    RESP,na,WaveQuality,Integer,IntelliVue,data
    RESP,na,WaveQuality,Integer,IntelliVue,index
    RR,na,Numeric,Float,IntelliVue,data
    RR,na,Numeric,Float,IntelliVue,index
    RR,na,Numeric,Float,IntelliVue,settings
    RR,na,NumericQuality,Integer,IntelliVue,data
    RR,na,NumericQuality,Integer,IntelliVue,index
    SpO2,na,Numeric,Float,IntelliVue,data
    SpO2,na,Numeric,Float,IntelliVue,index
    SpO2,na,Numeric,Float,IntelliVue,settings
    SpO2,na,NumericQuality,Integer,IntelliVue,data
    SpO2,na,NumericQuality,Integer,IntelliVue,index
    Temp,na,Numeric,Float,IntelliVue,data
    Temp,na,Numeric,Float,IntelliVue,index
    Temp,na,Numeric,Float,IntelliVue,settings
    Temp,na,NumericQuality,Integer,IntelliVue,data
    Temp,na,NumericQuality,Integer,IntelliVue,index
    UserMeasurements.xml


### Exercise 1: What is pycns?

**pycns** is an open-source Python library developed by Dr. Baptiste Balanca and his team (Lyon, CRNL, France) to make it easy to work with high-resolution physiologic data collected by the Natus CNS Monitor. It provides simple, standardized tools for loading, navigating, and analyzing data from the Moberg Clinical Platform (MCP) and legacy CNS Monitor archives.

The library handles the complex structure of multimodal ICU recordings—including waveforms (EEG, ABP, ICP, ECG), trends, annotations, and metadata—so users can focus on interpretation rather than file formats. With pycns, clinicians and researchers can efficiently:
* Load patient recordings from CNS or MCP archives
* Access synchronized multimodal signals with proper timestamps
* View annotations such as spreading depolarizations or clinical events
* Prepare data for signal processing, feature extraction, or AI models

In short, pycns is the foundational tool that enables fast, reproducible neuroinformatics workflows using Moberg data—powering everything from simple signal review to advanced research and machine-learning applications.

Where to Learn More
* GitHub repository: https://github.com/samuelgarcia/pycns
* Documentation & examples: https://pycns.readthedocs.io/en/latest/examples.html

These resources provide detailed usage examples, API references, and tutorials to help you get started quickly.

### Exercise 2: Setup Environment
Import the necessary libraries and functions we will need for the rest of the exercises


```python
# install from github
!pip install git+https://github.com/samuelgarcia/pycns.git@0dec1319c3e06f3c33105f1200582f770f6e2458 > /dev/null
!pip install git+https://github.com/samuelgarcia/physio.git@4569869a3b1938d0fc35335ab71641873bae8639 > /dev/null

# import required libraries

# standard libraries
from datetime import datetime
from pathlib import Path

# third-party libraries
import numpy as np
import pandas as pd

# Lyon libraries
from custom_view_moberg_hcl import CSD_view
import pycns # Toolbox made in Lyon, CRNL, France, for reading and viewing Moberg raw data
```

      Running command git clone --filter=blob:none --quiet https://github.com/samuelgarcia/pycns.git /tmp/pip-req-build-ac8wra91
      Running command git rev-parse -q --verify 'sha^0dec1319c3e06f3c33105f1200582f770f6e2458'
      Running command git fetch -q https://github.com/samuelgarcia/pycns.git 0dec1319c3e06f3c33105f1200582f770f6e2458
      Running command git clone --filter=blob:none --quiet https://github.com/samuelgarcia/physio.git /tmp/pip-req-build-zq_zxoxa
      Running command git rev-parse -q --verify 'sha^4569869a3b1938d0fc35335ab71641873bae8639'
      Running command git fetch -q https://github.com/samuelgarcia/physio.git 4569869a3b1938d0fc35335ab71641873bae8639
      Running command git checkout -q 4569869a3b1938d0fc35335ab71641873bae8639


### Exercise 3: Data Loading

Initalize the patient by setting `patient_data_folder` to the path of a Natus CNS Archive. After going through this once, you can come back to this section to load in a different folder by exploring the directory structure in Exercise 1.


```python
patient_data_folder = f"/mnt/s3/tracktbi-data-main/35/cns/Patient_2017_Nov_28__19_45_15_437001"
patient_data_folder = Path(patient_data_folder)
```

Load data from Natus CNS Monitor format using the `pycns` library, which provides a `CnsReader` object.


```python
reader = pycns.CnsReader(patient_data_folder,  with_events = True, event_time_zone = 'America/New_York')
```

The Natus CNS Monitor saves ICU signals in a special archive format. This format is:
* Highly optimized for time series data — it allows very fast access to high-frequency waveforms and long-duration recordings.
* Structured — it preserves both the signal values (e.g., ICP, ABP, EEG) and important metadata such as start times, stop times, and sampling frequencies.

Not all measurements are continuous. They may start and stop due to:
* Disconnections
* Patient transport
* Device reconfiguration

pycns gives us the tools to:
* Read data from CNS Monitor archives into Python.
* Extract metadata (signal names, durations, devices, sampling rates).

#### Why this matters:

Without pycns, CNS data would be difficult to handle because of the size and complexity of the files. With it, we can quickly pull exactly the data we need for analysis, whether that’s a single patient’s ICP trace or a whole cohort’s monitoring durations.

From the pycns package, we can pull:
* Measurement name (e.g., ICP, ABP, SpO₂)
* Sampling frequency
* Number of samples

This provides a quick summary of data coverage for the study population.

#### Events:

Let's look at annotated events for this patient archive.


```python
events = pd.DataFrame(reader.events) # Metadata from Dr. Rachel Thomas (Barrow)
events.head(20)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>name</th>
      <th>start_time</th>
      <th>duration</th>
      <th>description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Start</td>
      <td>2017-11-28 23:28:07.054</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>1</th>
      <td>1:x1=D5 X2=D6 Y1=d7 Y2=D8</td>
      <td>2017-11-28 23:32:15.118</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>2</th>
      <td>SD1</td>
      <td>2017-11-28 23:33:59.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>2:all plugged correctly now</td>
      <td>2017-11-28 23:37:17.135</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>SD1</td>
      <td>2017-11-28 23:38:29.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>5</th>
      <td>SD2</td>
      <td>2017-11-29 00:01:09.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>6</th>
      <td>SD2</td>
      <td>2017-11-29 00:05:09.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>7</th>
      <td>SD3</td>
      <td>2017-11-29 00:26:09.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>8</th>
      <td>SD3</td>
      <td>2017-11-29 00:31:29.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>9</th>
      <td>SD4</td>
      <td>2017-11-29 00:50:09.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>10</th>
      <td>SD4</td>
      <td>2017-11-29 00:55:09.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>11</th>
      <td>SD5</td>
      <td>2017-11-29 01:11:19.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>12</th>
      <td>SD6</td>
      <td>2017-11-29 01:35:59.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>13</th>
      <td>SD6</td>
      <td>2017-11-29 01:41:29.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>14</th>
      <td>SD7</td>
      <td>2017-11-29 02:02:19.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>15</th>
      <td>SD7</td>
      <td>2017-11-29 02:07:29.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>16</th>
      <td>SD8</td>
      <td>2017-11-29 02:27:29.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>17</th>
      <td>SD8</td>
      <td>2017-11-29 02:31:59.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>18</th>
      <td>SD9</td>
      <td>2017-11-29 02:52:19.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
    <tr>
      <th>19</th>
      <td>SD9</td>
      <td>2017-11-29 02:56:49.912</td>
      <td>0.0</td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>



#### Streams

Let's look at measurement data streams for this patient archive.


```python
reader.streams
```




    {'ABP_Dias': CnsStream ABP_Dias  rate:1Hz  shape:(131769,),
     'ABP_Mean': CnsStream ABP_Mean  rate:1Hz  shape:(136420,),
     'ABP_Syst': CnsStream ABP_Syst  rate:1Hz  shape:(131769,),
     'ABP': CnsStream ABP  rate:125Hz  shape:(17607904,),
     'CPP': CnsStream CPP  rate:1Hz  shape:(135742,),
     'ECG_II': CnsStream ECG_II  rate:500Hz  shape:(70431616,),
     'EEG_Impedance': CnsStream EEG_Impedance  rate:1Hz  shape:(140929, 32),
     'EEG_NeonatalParams': CnsStream EEG_NeonatalParams  rate:2Hz  shape:(281851, 16),
     'EEG': CnsStream EEG  rate:256Hz  shape:(36077439, 29),
     'EtCO2': CnsStream EtCO2  rate:1Hz  shape:(136404,),
     'HR': CnsStream HR  rate:1Hz  shape:(137375,),
     'ICP_Mean': CnsStream ICP_Mean  rate:1Hz  shape:(137217,),
     'ICP': CnsStream ICP  rate:125Hz  shape:(17607904,),
     'NBP_Dias': CnsStream NBP_Dias  rate:1Hz  shape:(132792,),
     'NBP_Mean': CnsStream NBP_Mean  rate:1Hz  shape:(132792,),
     'NBP_Syst': CnsStream NBP_Syst  rate:1Hz  shape:(136944,),
     'PLETH': CnsStream PLETH  rate:125Hz  shape:(17607904,),
     'PerfK_na_SparseNumeric': CnsStream PerfK_na_SparseNumeric  rate:100000Hz  shape:(1,),
     'PerfPPA_na_SparseNumeric': CnsStream PerfPPA_na_SparseNumeric  rate:100000Hz  shape:(1,),
     'RESP': CnsStream RESP  rate:62Hz  shape:(8803952,),
     'RR': CnsStream RR  rate:1Hz  shape:(136147,),
     'SpO2': CnsStream SpO2  rate:1Hz  shape:(137475,),
     'Tcore': CnsStream Tcore  rate:1Hz  shape:(136237,),
     'Tperf': CnsStream Tperf  rate:1Hz  shape:(137715,),
     'UVP_Mean': CnsStream UVP_Mean  rate:1Hz  shape:(137449,),
     'UVP': CnsStream UVP  rate:125Hz  shape:(17607904,),
     'perfDeltaT': CnsStream perfDeltaT  rate:1Hz  shape:(131095,)}



### Exercise 4: Review Data

This interactive viewer lets you explore high-resolution physiologic data loaded through **pycns**. Use the controls below to navigate, change signals, and adjust the display.

#### 1. Selecting which signals to display

You control which measurements are shown by editing the `stream_names` argument:

```python
w = pycns.get_viewer(reader, stream_names=['ICP'])
```

You may supply **multiple signals**, for example:

```python
w = pycns.get_viewer(reader, stream_names=['ICP', 'ABP', 'ECG'])
```

The widget will update automatically based on the streams you choose.

#### 2. Navigating using the timeline

Use the **timeline slider at the bottom** of the widget to move forward or backward through the recording.
This is the easiest way to browse long recordings and quickly jump to different time periods.

#### 3. Adjusting the window size (IMPORTANT)

The visible window of data can be changed using the **`win (s)` input box**:

* Type the desired window length in seconds (e.g., `600`, `3600`, `30`).
* Press **Enter** to apply.

⚠️ **Do NOT use the dropdown menu next to the arrow buttons to change the window size.**
That dropdown is deprecated and may not reflect the actual window being drawn.
Always use **`win (s)`** instead.

#### 4. Showing or hiding measurements

Under the **`options`** tab, you can toggle individual measurements on or off.

* This is helpful when you have multiple streams displayed (e.g., ICP + ABP + ECG).
* Use the checkboxes to focus on a specific physiologic signal without recreating the widget.

#### 5. Autoscale and display controls

* **Autoscale** (left sidebar) automatically adjusts the y-axis to fit the visible data.
* The toolbar on the left also allows panning, zooming, resetting the view, and exporting the figure.


```python
%matplotlib widget

ext_plots = {
    'CSD_mono': CSD_view(reader, with_detections=False, mode = 'mono', dc_band = (0.001, 0.025), ac_band = (0.5, 70), down_sampling_factor = 2, reref_median_ac = True, notch = True, offset_split_factor_uV = 1000, dc_gain_factor = 0.25, ac_gain_factor = 2),
    'CSD_bipol': CSD_view(reader, with_detections=False, mode = 'bipol', dc_band = (0.001, 0.025), ac_band = (0.5, 70), down_sampling_factor = 2, reref_median_ac = False, notch = True, offset_split_factor_uV = 1000, dc_gain_factor = 0.1, ac_gain_factor = 2),
}


w = pycns.get_viewer(reader, stream_names=['ABP_Mean', 'ICP_Mean', 'HR', 'RR', 'SpO2'], ext_plots=ext_plots, with_events=True)
w
```




    Viewer(children=(VBox(children=(HBox(children=(Button(description='autoscale', icon='refresh', style=ButtonSty…




```python
%matplotlib widget

w = pycns.get_viewer(reader, stream_names=['ABP', 'ICP', 'ECG_II'], with_events=True)
w
```




    Viewer(children=(VBox(children=(HBox(children=(Button(description='autoscale', icon='refresh', style=ButtonSty…



## End of Notebook 1
At this point, participants should be able to:
* Understand how different physiologic data types are organized in the Moberg Clinical Platform (trends vs. waveforms, metadata, timestamps).
* Load high-frequency physiological data (EEG, ICP, ABP, ECG, respiratory signals) from patient archives using pycns.
* Navigate, inspect, and validate patient recordings to ensure signals are aligned, complete, and ready for analysis.
* Explain why proper data handling is essential for downstream tasks such as event detection, feature extraction, and AI modeling.

## What’s Next?

In Notebook 2, we will build on what we learned here by diving deeper into event detection. Specifically, we’ll learn how to:
* Detect ECG R-peaks using standard signal-processing methods and evaluate beat-detection accuracy.
* Identify respiratory cycles from respiratory waveforms and compute basic breathing-rate features.
* Review and assess signal quality to determine when detections may be unreliable or require correction.
* Generate summary reports to evaluate patient physiologic stability.

This transition will move us from finding and loading data (Notebook 1) into detecting critical events (Notebook 2).
