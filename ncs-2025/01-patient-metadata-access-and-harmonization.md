# The 2025 Informatics, Big Data, and AI Workshop
NCS 23rd Annual Meeting \
September 18, 2025 in Montréal, Québec.

## Notebook 1 - Patient Metadata Access and Harmonization

**For more information or help, please email support@moberganalytics.com**

In this notebook, we will learn how to find, access, and harmonize patient monitoring data from a large study cohort. The examples are drawn from the TRACK-TBI dataset, which contains ICU monitoring data collected across multiple hospitals. This notebook can be applied to other data sets that are stored on the Moberg Clinical Platform (MCP).

Our goals are to:
* Locate the patient data.
* See what types of data are available for each patient.
* Extract metadata such as start/end times and durations.
* Summarize monitoring coverage across the cohort.
* Load example signals and begin exploring the differences between monitoring devices.

### 1. Finding TRACK-TBI Data

**Goal: Understand where the data lives and how it is organized.**

Patient archives on MCP are stored in a folder system, much like the folders and files you use every day on your own computer.

At the very top, we have one main folder that holds everything:

`/mnt/s3/tracktbi-data-main`

Inside this folder, the first thing you see are patient identifiers.
* Each patient has a unique identifier, called a patient ID (e.g.,, 1, 2).
* This IDs ensures that each patient’s data is kept separate and distinct from every other patient.


Under each patient’s folder, the data is divided into subfolders by type:
* cns/ → Moberg CNS monitor archives
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

This path corresponds to a Moberg CNS Monitor archive called `Patient_2014_Dec_16__12_48_34_830639` for patient ID `1`.

This organization allows us to quickly find the raw files for each patient.

Execute the commands below to explore the TRACK-TBI database. Feel free to make changes to the following commands to explore the database. For example, you can change `1` in `!ls /mnt/s3/tracktbi-data-main/1` to `10` to view the contents of a different patient. 


```python
!ls /mnt/s3/tracktbi-data-main
```

    1    106  113  120  129  15   162  24  32  4   47  55  62  70  80  88  95
    10   107  114  121  13	 153  163  25  33  40  48  56  64  71  81  89  96
    100  108  115  123  130  156  17   26  34  41  5   57  65  72  82  9   97
    101  109  116  124  131  158  18   27  35  42  50  58  66  73  83  90  98
    102  11   117  125  132  159  19   28  36  43  51  59  67  75  84  91  99
    103  110  118  126  133  16   21   29  37  44  52  6   68  76  85  92
    104  111  119  127  134  160  22   3   38  45  53  60  69  78  86  93
    105  112  12   128  14	 161  23   30  39  46  54  61  7   8   87  94



```python
!ls /mnt/s3/tracktbi-data-main/1
```

    cns



```python
!ls /mnt/s3/tracktbi-data-main/1/cns
```

    Patient_2014_Dec_16__12_48_34_830639



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


### 2. Generating a List of Data Labels

**Goal: See which data streams exist for which patients.**

Each patient may have a different set of measurements depending on the devices connected at the time. For example:
* Some may have ICP (intracranial pressure) but no PbtO2.
* Some may have ICP labeled ICP1 and others may have it labeled IC2.
* Some may have EEG channels while others do not.
* ...and many other configurations

Because of this, many analyses may have missing data.

We will generate an N × M matrix:
* Rows = patients
* Columns = data labels (e.g., ICP, ABP, EEG, SpO₂, etc.)
* Values = present (True) or missing (False)

This “measurement matrix” helps us decide which analyses are possible across the cohort and what inclusion/exclusion criteria to use for patient selection.

Execute the commands below to create the measurement matrix.

You can change the following:
* `MAX_FILES` to change the maximum number of patients that are processed. The large the value, the longer it will take to build.


```python
from glob import glob

import pandas as pd

from cns_utils.readCNS import CNSDataSource

# TODO: Edit me to change how many patients are included in the matrix
MAX_FILES = 5

measurement_list = []

files = glob('/mnt/s3/tracktbi-data-main/*/cns/Patient_*')

nfiles = len(files)

for i, file in enumerate(files):
    if i > MAX_FILES - 1:
        break
        
    print(f'[{i + 1}/{nfiles}] Processing {file}...')
    cns_data_source = CNSDataSource(file)

    basenames = cns_data_source.get_basenames()

    eeg_channels = None
    
    try:
        eeg_reviewer = cns_data_source.get_modality_data_reviewer('EEG,Composite,SampleSeries')
        eeg_settings = eeg_reviewer.settings()
        eeg_channels = list(eeg_settings['ch_names'].keys())
    except:
        pass

    measurements = ['_'.join(e.split(',')[0:2]) for e in basenames]

    measurement_dict = {
        'file': file, 
        **{measurement: True for measurement in measurements}
    }
    if eeg_channels:
        measurement_dict = {
        **measurement_dict,
        **{eeg_channel: True for eeg_channel in eeg_channels}
    }
    
    measurement_list.append(measurement_dict)

    measurement_list
        
df = pd.DataFrame(measurement_list)
df = df.fillna(False)
df.index = df['file']
df = df.drop(['file'], axis=1)

print(df)
```

    [1/274] Processing /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_Dec_16__12_48_34_830639...
    [2/274] Processing /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_Jul_14__15_27_46_984268...
    [3/274] Processing /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247...
    [4/274] Processing /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253...
    [5/274] Processing /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1258_01...
                                                        ART_Mean  ABP_Mean  \
    file                                                                     
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...      True      True   
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...      True      True   
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247     False      True   
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253     False      True   
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...     False      True   
    
                                                        ART_na  ABP_Syst  CVP_na  \
    file                                                                           
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...    True      True    True   
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...    True      True    True   
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247   False      True    True   
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253   False      True   False   
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...   False      True   False   
    
                                                        NBP_Mean  CPP_na  \
    file                                                                   
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...      True    True   
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...      True    True   
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247      True    True   
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253      True   False   
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...      True    True   
    
                                                        PLETH_na  ICP_na  Temp_na  \
    file                                                                            
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...      True    True     True   
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...      True    True     True   
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247     False    True    False   
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253     False   False    False   
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...     False    True    False   
    
                                                        ...  ICT_na  ECG_II  \
    file                                                ...                   
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...  ...    True    True   
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...  ...    True    True   
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247  ...   False   False   
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253  ...   False   False   
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...  ...   False   False   
    
                                                        NBP_Syst  SpO2_na  IC1_na  \
    file                                                                            
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...      True     True   False   
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...      True     True   False   
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247      True     True    True   
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253      True     True   False   
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...      True     True   False   
    
                                                        IC1_Mean  EtCO2_na  \
    file                                                                     
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...     False     False   
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...     False     False   
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247      True      True   
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253     False     False   
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...     False      True   
    
                                                        LAP_Mean  LAP_na  Tcore_na  
    file                                                                            
    /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_D...     False   False     False  
    /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_...     False   False     False  
    /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247      True    True     False  
    /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253     False   False     False  
    /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1...     False   False      True  
    
    [5 rows x 31 columns]


One we have the measurement matrix, it's very easy to see what patients have which measurements. 

The following measurements are available from at least one patient. 

Execute the commands below to list the measurements available.


```python
print(list(df.columns))
```

    ['ART_Mean', 'ABP_Mean', 'ART_na', 'ABP_Syst', 'CVP_na', 'NBP_Mean', 'CPP_na', 'PLETH_na', 'ICP_na', 'Temp_na', 'ABP_Dias', 'NBP_Dias', 'ART_Syst', 'RESP_na', 'CVP_Mean', 'RR_na', 'ICP_Mean', 'HR_na', 'ABP_na', 'PbtO2_na', 'ART_Dias', 'ICT_na', 'ECG_II', 'NBP_Syst', 'SpO2_na', 'IC1_na', 'IC1_Mean', 'EtCO2_na', 'LAP_Mean', 'LAP_na', 'Tcore_na']


You can query the measurement matrix by choosing measurements of interest and then it will tell you what patient archives contain them. 

Execute the commands below to query the measurement matrix. Below we are querying for `IC1_Mean` and `Temp_na`. Here you see that these measurements are sparsely available. 

Feel free to make changes to the following commands to query other measurements. For example, you can change `IC1_Mean` to `ICP_Mean` or something that doesn't exist (hint: the command will error if you query for something that doesn't exist).


```python
df[['ICP_Mean', 'Temp_na']]
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
      <th>ICP_Mean</th>
      <th>Temp_na</th>
    </tr>
    <tr>
      <th>file</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>/mnt/s3/tracktbi-data-main/1/cns/Patient_2014_Dec_16__12_48_34_830639</th>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>/mnt/s3/tracktbi-data-main/10/cns/Patient_2015_Jul_14__15_27_46_984268</th>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>/mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247</th>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>/mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253</th>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>/mnt/s3/tracktbi-data-main/102/cns/Patient_03-1258_01</th>
      <td>True</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



### 3. Extracting Metadata (Start/End Times)

**Goal: For a single patient, extract all available metadata about each signal.**

#### Background:

`cns-utils` is an internally maintained Python module by Moberg Analytics that we use to work with data from the Moberg CNS Monitor.

The CNS Monitor saves ICU signals in a special archive format. This format is:
* Highly optimized for time series data — it allows very fast access to high-frequency waveforms and long-duration recordings.
* Structured — it preserves both the signal values (e.g., ICP, ABP, EEG) and important metadata such as start times, stop times, and sampling frequencies.

Not all measurements are continuous. They may start and stop due to:
* Disconnections
* Patient transport
* Device reconfiguration

cns-utils gives us the tools to:
* Read data from CNS Monitor archives into Python.
* Write data back into the archive format if we want to share or re-use it.
* Extract metadata (signal names, durations, devices, sampling rates).

#### Why this matters:

Without cns-utils, CNS data would be difficult to handle because of the size and complexity of the files. With it, we can quickly pull exactly the data we need for analysis, whether that’s a single patient’s ICP trace or a whole cohort’s monitoring durations.

From the cns-utils package, we can pull:
* Measurement name (e.g., ICP, ABP, SpO₂)
* Start time and end time
* Total duration of monitoring
* Source device (EVD monitor, intraparenchymal probe, bedside monitor, etc.)
* Sampling frequency

This provides a quick summary of data coverage for the study population.

Execute the commands below to extract metadata for a single patient. 

You can change the following:
* `TIMEZONE` to change the timezone that the time information is displayed in.
* `CNS_ARCHIVE` to change what CNS archive is used (hint: use the measurement matrix from above).


```python
from cns_utils import CNSDataSource

from datetime import datetime, timezone, timedelta
import pytz

# TODO: Change the timezone to see how it affects the output
TIMEZONE = pytz.timezone("US/Eastern")

# TODO: Change the CNS archive path to another directory to see how it affects the output
CNS_ARCHIVE = '/mnt/s3/tracktbi-data-main/156/cns/Patient_2017_Nov_28__19_45_15_437001'

cns_data_source = CNSDataSource(CNS_ARCHIVE)

basenames = cns_data_source.get_basenames()

nbasenames = len(basenames)

for i, basename in enumerate(sorted(basenames)):
    
    if 'EEG,Composite,Impedance,' in basename or 'EEG,Composite,NeonatalParams,' in basename:
        continue
    
    reviewer = cns_data_source.get_modality_data_reviewer(basename)
    reviewer_settings = reviewer.settings()
    units = reviewer_settings.get('units')
    measurement_name = '_'.join(basename.split(',')[0:2])
    source = basename.split(',')[4]
    sampling_type = basename.split(',')[2]
    data_diration = round(reviewer.data_duration() / 1e6 / 60 / 60 / 24, 2)
    start_time = reviewer.start_time()
    end_time = reviewer.end_time()
    total_samples = reviewer.total_samples()
    df = reviewer.review_data_with_interval(0, 100)
    df_metadata = df['metadata']
    estimated_sampling_frequency = round(df['num_samples'] / (df_metadata['end_time'] - df_metadata['start_time']) * 1e6, 2)
    
    start_time_s = start_time / 1_000_000
    end_time_s = end_time / 1_000_000

    start_time_utc = datetime.fromtimestamp(start_time_s, tz=timezone.utc)
    end_time_utc = datetime.fromtimestamp(end_time_s, tz=timezone.utc)

    start_time_est = start_time_utc.astimezone(TIMEZONE)
    start_time_str = start_time_est.strftime("%Y-%m-%d %H:%M:%S")
    
    end_time_est = end_time_utc.astimezone(TIMEZONE)
    end_time_str = end_time_est.strftime("%Y-%m-%d %H:%M:%S")
    
    print(f'[{i + 1}/{nbasenames}]\tMeasurement: {measurement_name}\t\tSource: {source}\t\tSampling Type: {sampling_type}')
    print(f'\tStart Time: {start_time_str}\tEnd Time: {end_time_str}\tDuration: {data_diration} days')
    print(f'\tTotal Samples: {total_samples}\t\tSampling Frequency: {estimated_sampling_frequency} Hz\tUnits: {units}')
```

    [1/26]	Measurement: ABP_Dias		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.56 days
    	Total Samples: 131769		Sampling Frequency: 1.12 Hz	Units: mmHg
    [2/26]	Measurement: ABP_Mean		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.62 days
    	Total Samples: 136420		Sampling Frequency: 1.12 Hz	Units: mmHg
    [3/26]	Measurement: ABP_Syst		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.56 days
    	Total Samples: 131769		Sampling Frequency: 1.12 Hz	Units: mmHg
    [4/26]	Measurement: ABP_na		Source: IntelliVue		Sampling Type: SampleSeries
    	Start Time: 2017-11-28 14:45:17	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 17607904		Sampling Frequency: 126.2 Hz	Units: mmHg
    [5/26]	Measurement: CPP_na		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.61 days
    	Total Samples: 135742		Sampling Frequency: 1.12 Hz	Units: mmHg
    [6/26]	Measurement: ECG_II		Source: IntelliVue		Sampling Type: SampleSeries
    	Start Time: 2017-11-28 14:45:17	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 70431616		Sampling Frequency: 504.8 Hz	Units: mV
    [9/26]	Measurement: EEG_Composite		Source: Amp1020		Sampling Type: SampleSeries
    	Start Time: 2017-11-28 14:45:16	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 36077440		Sampling Frequency: 258.56 Hz	Units: uV
    [10/26]	Measurement: EtCO2_na		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.62 days
    	Total Samples: 136404		Sampling Frequency: 1.12 Hz	Units: mmHg
    [11/26]	Measurement: HR_na		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 137375		Sampling Frequency: 1.12 Hz	Units: bpm
    [12/26]	Measurement: ICP_Mean		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 137217		Sampling Frequency: 1.12 Hz	Units: mmHg
    [13/26]	Measurement: ICP_na		Source: IntelliVue		Sampling Type: SampleSeries
    	Start Time: 2017-11-28 14:45:17	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 17607904		Sampling Frequency: 126.2 Hz	Units: mmHg
    [14/26]	Measurement: NBP_Dias		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:53:49	Duration: 1.57 days
    	Total Samples: 132792		Sampling Frequency: 1.12 Hz	Units: mmHg
    [15/26]	Measurement: NBP_Mean		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:53:49	Duration: 1.57 days
    	Total Samples: 132792		Sampling Frequency: 1.12 Hz	Units: mmHg
    [16/26]	Measurement: NBP_Syst		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.62 days
    	Total Samples: 136944		Sampling Frequency: 1.12 Hz	Units: mmHg


    /tmp/ipykernel_152/3068387260.py:35: RuntimeWarning: divide by zero encountered in scalar divide
      estimated_sampling_frequency = round(df['num_samples'] / (df_metadata['end_time'] - df_metadata['start_time']) * 1e6, 2)


    [17/26]	Measurement: PerfK_na		Source: Bowman		Sampling Type: SparseNumeric
    	Start Time: 2017-11-28 14:45:16	End Time: 2017-11-28 14:45:16	Duration: 0.0 days
    	Total Samples: 1		Sampling Frequency: inf Hz	Units: none
    [18/26]	Measurement: PerfPPA_na		Source: Bowman		Sampling Type: SparseNumeric
    	Start Time: 2017-11-28 14:45:16	End Time: 2017-11-28 14:45:16	Duration: 0.0 days
    	Total Samples: 1		Sampling Frequency: inf Hz	Units: none
    [19/26]	Measurement: RESP_na		Source: IntelliVue		Sampling Type: SampleSeries
    	Start Time: 2017-11-28 14:45:17	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 8803952		Sampling Frequency: 63.1 Hz	Units: Ohms
    [20/26]	Measurement: RR_na		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.61 days
    	Total Samples: 136147		Sampling Frequency: 1.12 Hz	Units: BPM
    [21/26]	Measurement: SpO2_na		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 137475		Sampling Frequency: 1.12 Hz	Units: %
    [22/26]	Measurement: Tcore_na		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.62 days
    	Total Samples: 136237		Sampling Frequency: 1.12 Hz	Units: °C
    [23/26]	Measurement: Tperf_na		Source: Bowman		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:00	End Time: 2017-11-30 05:54:03	Duration: 1.58 days
    	Total Samples: 137715		Sampling Frequency: 1.14 Hz	Units: °C
    [24/26]	Measurement: UVP_Mean		Source: IntelliVue		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:01	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 137449		Sampling Frequency: 1.12 Hz	Units: mmHg
    [25/26]	Measurement: UVP_na		Source: IntelliVue		Sampling Type: SampleSeries
    	Start Time: 2017-11-28 14:45:17	End Time: 2017-11-30 05:54:02	Duration: 1.63 days
    	Total Samples: 17607904		Sampling Frequency: 126.2 Hz	Units: mmHg
    [26/26]	Measurement: perfDeltaT_na		Source: Bowman		Sampling Type: Numeric
    	Start Time: 2017-11-28 14:45:00	End Time: 2017-11-30 05:54:03	Duration: 1.51 days
    	Total Samples: 131095		Sampling Frequency: 1.14 Hz	Units: °C


With these values, we can also calculate cohort-wide statistics, such as:
* Median monitoring duration
* Interquartile range (IQR)

Execute the commands below to visualize data for a patient. 

You can change the following:
* `MAX_FILES` to change the maximum number of patients that are processed. The large the value, the longer it will take to build.


```python
from glob import glob

import pandas as pd
import numpy as np

from cns_utils.readCNS import CNSDataSource

# TODO: Edit me to change how many patients are included in the matrix
MAX_FILES = 5

hour_dict = {}

files = glob('/mnt/s3/tracktbi-data-main/*/cns/Patient_*')

nfiles = len(files)

for i, file in enumerate(files):
    if i > MAX_FILES - 1:
        break
        
    print(f'[{i + 1}/{nfiles}] Processing {file}...')
    cns_data_source = CNSDataSource(file)

    duration_hours = (cns_data_source.end_time() - cns_data_source.start_time()) / 1e6 / 60 / 60
    hour_dict[file] = duration_hours
    print(f'\tDuration: {round(duration_hours, 2)} hours')
    
print('')
hour_list = list(hour_dict.values())
median = np.median(hour_list)
q1 = np.percentile(hour_list, 25)
q3 = np.percentile(hour_list, 75)
print(f'Median: {round(median, 2)}\t[{round(q1, 2)}, {round(q3, 2)}]')
```

    [1/274] Processing /mnt/s3/tracktbi-data-main/1/cns/Patient_2014_Dec_16__12_48_34_830639...
    	Duration: 571.59 hours
    [2/274] Processing /mnt/s3/tracktbi-data-main/10/cns/Patient_2015_Jul_14__15_27_46_984268...
    	Duration: 19.13 hours
    [3/274] Processing /mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247...
    	Duration: 746.97 hours
    [4/274] Processing /mnt/s3/tracktbi-data-main/101/cns/Patient_03-1253...
    	Duration: 257.81 hours
    [5/274] Processing /mnt/s3/tracktbi-data-main/102/cns/Patient_03-1258_01...
    	Duration: 123.39 hours
    
    Median: 257.81	[123.39, 571.59]


### 4. Loading Data

**Goal: Load and visualize monitoring data for one or more patients.**

#### Why review signals this way?

When you’re aggregating multi-site ICU data, quick interactive review helps you:
* Spot gaps & transports: identify start/stop periods, line disconnects, or moves between units.
* Catch device/config differences: e.g., EVD vs IPM ICP, arterial line vs cuff ABP, channel naming quirks.
* Verify units & scaling: mmHg vs kPa, saturation/clipping, mis-scaled values.
* See sampling-rate or clock issues: rate changes, clock drift, timezone mistakes.
* Detect artifacts: flushes, zeroing, calibration, electrical noise, lead swaps.
* Confirm alignment: ensure ICP aligns with ABP, meds, and events before any cohort analysis.

A 60-second visual check prevents hours of downstream cleanup and avoids biased cohort statistics.

We will again use cns-utils to load raw time series into Python.
Example steps:
* Load ICP data for one patient.
* Plot it over time

Execute the commands below to visualize data for a patient. 

You can change the following:
* `TIMEZONE` to change the timezone that the time information is displayed in.
* `CNS_ARCHIVE` to change what CNS archive is used (hint: use the measurement matrix from above).
* `BASENAME` to change the measurement that is displayed (hint: use the measurement matrix from above).


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.dates as mdates

# TODO: Change the timezone to see how it affects the output
TIMEZONE = pytz.timezone("US/Eastern")

# TODO: Change the CNS archive path to another directory to see how it affects the output
CNS_ARCHIVE = '/mnt/s3/tracktbi-data-main/153/cns/Patient_1_w_ICMP'

# TODO: Change the basename to pull in different types of data
BASENAME = 'ICP,na'

measurement_name = '_'.join(BASENAME.split(',')[0:2])

cns_data_source = CNSDataSource(CNS_ARCHIVE)

reviewer = cns_data_source.get_modality_data_reviewer(BASENAME)

reviewer_settings = reviewer.settings()
units = reviewer_settings.get('units')

df, _ = reviewer.review_data_frame(reviewer.start_time(), reviewer.end_time())

def to_datetime_index(frame: pd.DataFrame) -> pd.DataFrame:
    """Ensure a tz-aware UTC DateTimeIndex named 'time'."""
    f = frame.copy()
    if np.issubdtype(f.index.dtype, np.number):
        f.index = pd.to_datetime(f.index.astype('int64'), unit='us', utc=True)
    else:
        f.index = pd.to_datetime(f.index, utc=True, errors='coerce')
    f.index.name = 'time'
    return f

def pick_value_series(frame: pd.DataFrame) -> pd.Series:
    """Return a single numeric Series to plot."""
    if isinstance(frame, pd.Series):
        return frame
    if 'value' in frame.columns:
        return frame['value']
    num_cols = frame.select_dtypes(include='number').columns
    if len(num_cols) == 0:
        raise ValueError("No numeric columns found to plot.")
    return frame[num_cols[0]]

df = to_datetime_index(df)
if df.index.tz is None:
    df.index = df.index.tz_localize('UTC')
df = df.tz_convert(TIMEZONE)

s_data = pick_value_series(df).resample('1S').median()

s_data = s_data.clip(lower=-5, upper=200)

def list_available_days(s: pd.Series):
    """Return a list of YYYY-MM-DD strings covered by the series."""
    start = s.index[0].normalize()
    end = s.index[-1].normalize()
    days = pd.date_range(start, end, freq='D', tz=s.index.tz)
    return [d.strftime('%Y-%m-%d') for d in days]

def plot_day(s: pd.Series, day_str: str, shade_gaps=True, ylim=(None, None)):
    """
    Plot one day of data with nice formatting.
      s: resampled Series with tz-aware DateTimeIndex
      day_str: 'YYYY-MM-DD' in local tz of s
    """
    day = pd.Timestamp(day_str).tz_localize(s.index.tz)
    start = day
    end = day + pd.Timedelta(days=1)

    seg = s.loc[start:end]

    fig, ax = plt.subplots(figsize=(11, 4))
    ax.plot(seg.index, seg.values, linewidth=0.7)
    ax.set_title(f"{measurement_name} over time — {day_str} ({s.index.tz})  [{len(seg):,} points]")
    ax.set_ylabel(f"{measurement_name} ({units})")
    ax.set_xlabel("Clock time")
    if any(ylim):
        ax.set_ylim(*ylim)

    # Nice time axis
    ax.grid(True, linestyle='--', alpha=0.3)
    ax.xaxis.set_major_locator(mdates.AutoDateLocator())
    ax.xaxis.set_major_formatter(mdates.DateFormatter('%H:%M'))
    fig.autofmt_xdate()

    # Shade 1-minute bins with no data (helps QA spot dropouts/transports)
    if shade_gaps:
        counts = s.resample('1min').count()
        gaps = counts[(counts == 0) & (counts.index >= start) & (counts.index <= end)]
        for t in gaps.index:
            ax.axvspan(t, t + pd.Timedelta(minutes=1), color='0.8', alpha=0.5)

    plt.tight_layout()
    return ax

days = list_available_days(s_data)
print("Available days:", days[:10], "...")  # print a few

plot_day(s_data, day_str=days[0])
```

    NO UNITS FOUND
    Available days: ['2009-09-07', '2009-09-08'] ...





    <Axes: title={'center': 'ICP_na over time — 2009-09-07 (US/Eastern)  [65,441 points]'}, xlabel='Clock time', ylabel='ICP_na (None)'>




    
![png](images/output_18_2.png)
    


### 5. Differentiating ICP Sources (EVD vs IPM)

**Goal: Distinguish data sources based on visual inspection.**

Intracranial pressure (ICP) can be measured in two main ways:
* EVD (External Ventricular Drain) – catheter into the ventricles
* IPM (Intraparenchymal Monitor) – probe into brain tissue

In the TRACK-TBI dataset, both appear under “ICP” but are recorded from different devices.

Task for the participants:
* Load two ICP examples (EVD vs IPM).
* Compare their signal patterns.
* Discuss which is which based on visual inspection.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.dates as mdates

TIMEZONE = pytz.timezone("US/Eastern")

# TODO: Change the CNS archive path to another directory to see how it affects the output
CNS_ARCHIVE = '/mnt/s3/tracktbi-data-main/100/cns/Patient_03-1247'

# TODO: Change the basename to pull in different types of data
BASENAME_ICP = 'ICP,na'
BASENAME_IC1 = 'IC1,na'

# TODO: Change the duration (minutes) to load in different amount of data
DURATION = 60

# TODO: Change the off set to start loading in data from a different region
START_OFFSET = 120

DURATION *= 1e6 * 60
START_OFFSET *= 1e6 * 60

measurement_name = '_'.join(BASENAME.split(',')[0:2])

cns_data_source = CNSDataSource(CNS_ARCHIVE)

icp_reviewer = cns_data_source.get_modality_data_reviewer(BASENAME_ICP)
ic1_reviewer = cns_data_source.get_modality_data_reviewer(BASENAME_IC1)

icp_reviewer_settings = icp_reviewer.settings()
ic1_reviewer_settings = ic1_reviewer.settings()

icp_units = icp_reviewer_settings.get('units')
ic1_units = ic1_reviewer_settings.get('units')

icp_df, _ = icp_reviewer.review_data_frame(icp_reviewer.start_time() + START_OFFSET, icp_reviewer.start_time() + START_OFFSET + DURATION)
ic1_df, _ = ic1_reviewer.review_data_frame(ic1_reviewer.start_time() + START_OFFSET, ic1_reviewer.start_time() + START_OFFSET + DURATION)

def to_datetime_index(frame: pd.DataFrame) -> pd.DataFrame:
    """Ensure a tz-aware UTC DateTimeIndex named 'time'."""
    f = frame.copy()
    if np.issubdtype(f.index.dtype, np.number):
        f.index = pd.to_datetime(f.index.astype('int64'), unit='us', utc=True)
    else:
        f.index = pd.to_datetime(f.index, utc=True, errors='coerce')
    f.index.name = 'time'
    return f

def pick_value_series(frame: pd.DataFrame) -> pd.Series:
    """Return a single numeric Series to plot."""
    if isinstance(frame, pd.Series):
        return frame
    if 'value' in frame.columns:
        return frame['value']
    num_cols = frame.select_dtypes(include='number').columns
    if len(num_cols) == 0:
        raise ValueError("No numeric columns found to plot.")
    return frame[num_cols[0]]

icp_df = to_datetime_index(icp_df)
ic1_df = to_datetime_index(ic1_df)


if icp_df.index.tz is None:
    icp_df.index = icp_df.index.tz_localize('UTC')
    
if ic1_df.index.tz is None:
    ic1_df.index = ic1_df.index.tz_localize('UTC')
    
icp_df = icp_df.tz_convert(TIMEZONE)
ic1_df = ic1_df.tz_convert(TIMEZONE)

icp_s_data = pick_value_series(icp_df).resample('1S').median()
ic1_s_data = pick_value_series(ic1_df).resample('1S').median()

icp_s_data = icp_s_data.clip(lower=-5, upper=200)
ic1_s_data = ic1_s_data.clip(lower=-5, upper=200)

def list_available_days(s: pd.Series):
    """Return a list of YYYY-MM-DD strings covered by the series."""
    start = s.index[0].normalize()
    end = s.index[-1].normalize()
    days = pd.date_range(start, end, freq='D', tz=s.index.tz)
    return [d.strftime('%Y-%m-%d') for d in days]

def plot_day(s: pd.Series, day_str: str, shade_gaps=True, ylim=(None, None)):
    """
    Plot one day of data with nice formatting.
      s: resampled Series with tz-aware DateTimeIndex
      day_str: 'YYYY-MM-DD' in local tz of s
    """
    day = pd.Timestamp(day_str).tz_localize(s.index.tz)
    start = day
    end = day + pd.Timedelta(days=1)

    seg = s.loc[start:end]

    fig, ax = plt.subplots(figsize=(11, 4))
    ax.plot(seg.index, seg.values, linewidth=0.7)
    ax.set_title(f"{measurement_name} over time — {day_str} ({s.index.tz})  [{len(seg):,} points]")
    ax.set_ylabel(f"{measurement_name} ({units})")
    ax.set_xlabel("Clock time")
    if any(ylim):
        ax.set_ylim(*ylim)

    # Nice time axis
    ax.grid(True, linestyle='--', alpha=0.3)
    ax.xaxis.set_major_locator(mdates.AutoDateLocator())
    ax.xaxis.set_major_formatter(mdates.DateFormatter('%H:%M'))
    fig.autofmt_xdate()

    # Shade 1-minute bins with no data (helps QA spot dropouts/transports)
    if shade_gaps:
        counts = s.resample('1min').count()
        gaps = counts[(counts == 0) & (counts.index >= start) & (counts.index <= end)]
        for t in gaps.index:
            ax.axvspan(t, t + pd.Timedelta(minutes=1), color='0.8', alpha=0.5)

    plt.tight_layout()
    return ax

icp_days = list_available_days(icp_s_data)
ic1_days = list_available_days(ic1_s_data)

plot_day(icp_s_data, day_str=icp_days[0])
plot_day(ic1_s_data, day_str=ic1_days[0])
```




    <Axes: title={'center': 'ICP_na over time — 2017-01-13 (US/Eastern)  [3,601 points]'}, xlabel='Clock time', ylabel='ICP_na (None)'>




    
![png](images/output_20_1.png)
    



    
![png](images/output_20_2.png)
    


## End of Notebook 1
At this point, participants should be able to:
* Navigate the dataset structure.
* Build an availability matrix of data labels.
* Extract and summarize monitoring metadata.
* Load and visualize example signals.
* Recognize the difference between EVD and IPM ICP sources.

## What’s Next?

In Notebook 2, we will build on what we learned here by diving deeper into data visualization and exploratory analysis. Specifically, we’ll learn how to:

* Convert Moberg CNS archives to HDF5
* Understand the HDF5 structure
* Explore multichannel time series data stored in HDF5
* Load in other types of data to merge with HDF5

This transition will move us from finding and harmonizing data (Notebook 1) into handling and visualizing data (Notebook 2).
