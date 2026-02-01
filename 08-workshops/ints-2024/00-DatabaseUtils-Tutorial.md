# INTS Neuromonitoring Workshop - ICM+ and Moberg Analytics
## Moberg Analytics Sandbox - Lab Exercise 00
## Accessing Patient Metadata and Annotations with DatabaseUtils

**For more information or help, please email support@moberganalytics.com**


<h2>What is DatabaseUtils?</h2>
DatabaseUtils is a class which has a lot of methods which will allow you to access Moberg patient data stored in the Moberg Database. For this tutorial we will show how we can access patient data and annotations

<h2>Importing Database Utils</h2>

The Moberg Server comes installed with python library database_utils. The code below shows how it can be imported


```python
from database_utils import DatabaseUtils
```

We will also import some other python libraries we will be using throughout this tutorial


```python
# now we'll import some libraries we'll use throughout this tutorial
from sam_tools.utils import view_json
import os
```

    Requirement already satisfied: json2html in /opt/conda/lib/python3.9/site-packages (1.3.0)


<h2>Initializing DatabaseUtilsObject</h2>

The first step will be getting the access_token, there are two ways of doing that: 

<h3>a) Using Jupyterhub API</h3>
The first method will be through accessing jupyerhub's api. The function below will use jupyterhub api to get the access_token


```python
from database_utils import jhub_get_access_token
jhub_get_access_token()
```




    '21e9b6d9eff13cb25fbcf54a5b6d0f54d6a242fb4e6ed84a539f0ee214dd6a82e335d4974b9178930710d5fd178d4f60239a45f334f86af527e7d3846bbc5a7782abcccacb749e3e5ab3eff607bf90fb3d9572898105e27c743e257389ec28f1d2b40038'



<h3>b) Using Environment Variable</h3>
The access_token can also be accessed through environment variable 'AD_TOKEN' as shown below


```python
os.getenv('AD_TOKEN')
```




    '21e9b6d9eff13cb25fbcf54a5b6d0f54d6a242fb4e6ed84a539f0ee214dd6a82e335d4974b9178930710d5fd178d4f60239a45f334f86af527e7d3846bbc5a7782abcccacb749e3e5ab3eff607bf90fb3d9572898105e27c743e257389ec28f1d2b40038'



The first method is preferred since it will also check wether the token has expired or not.
Now that we have our access token, we are ready to initialize out DatabaseUtils object, as shown in the code below:


```python
# we first obtain the access token
token = jhub_get_access_token()

# then we initialize the DatabaseUtils object
database_utils_obj = DatabaseUtils(token)
```

<h2>Accessing Patient Data</h2>

All patient data can be accessed through the `get_all_patients` method:


```python
# this will retrieve all patients from the database
all_patients = database_utils_obj.get_all_patients()
print("There are", len(all_patients), "patients in the database")

# we can also print out the first patient's data as shown below
view_json(all_patients[0])
```

    47





<table border="1"><tr><th>annotation_count</th><td>0</td></tr><tr><th>annotation_ids</th><td><ul><li>None</li></ul></td></tr><tr><th>data_file_ids</th><td><ul><li>None</li></ul></td></tr><tr><th>data_file_name</th><td><ul><li>None</li></ul></td></tr><tr><th>data_file_size</th><td>0</td></tr><tr><th>end_time</th><td>None</td></tr><tr><th>first_name</th><td>Ethan</td></tr><tr><th>id</th><td>274</td></tr><tr><th>last_name</th><td>Moyer</td></tr><tr><th>patient_id</th><td>0000</td></tr><tr><th>patient_key</th><td>29</td></tr><tr><th>project_id</th><td>6</td></tr><tr><th>project_identifier</th><td>PROJ004</td></tr><tr><th>project_name</th><td>Project 4</td></tr><tr><th>site_id</th><td>8</td></tr><tr><th>site_identifier</th><td>0008</td></tr><tr><th>site_name</th><td>Moberg Analytics - Germany</td></tr><tr><th>start_time</th><td>None</td></tr><tr><th>status</th><td>None</td></tr><tr><th>timestamp</th><td>Tue, 14 May 2024 18:53:30 GMT</td></tr><tr><th>timezone</th><td><ul><li>None</li></ul></td></tr><tr><th>total_monitoring_time</th><td>None</td></tr></table>



<h3>Filtering Patients</h3>
Patient Data can be filtered by provinding a filter dictionary to `get_all_patients` method. The value provided will search on the basis of patient_identifier, site_identifier, site_name,project_identifier,  and project_name.


```python
# here, we filter for patients with "Moberg Analytics" in any of the fields
# the 'search_value' key will search across all the fields
moberg_patients = database_utils_obj.get_all_patients({"search_value": "Moberg Analytics"})
print(len(moberg_patients), "patients found with search value 'Moberg Analytics'")
```

    47


<h3>Sorting Patients</h3>
Patient Data can be sorted by proving a list of sort categories. Below is an example of sorting data based on first_name


```python
# here, we use a dictionary to sort the 'first_name' field in descending order
sorted_patients_0 = database_utils_obj.get_all_patients(sort=[
    {
        "field": "first_name",
        "sort": "desc"
    }
])

# view the first patient in the sorted list
view_json(sorted_patients_0[0])
```




<table border="1"><tr><th>annotation_count</th><td>0</td></tr><tr><th>annotation_ids</th><td><ul><li>None</li></ul></td></tr><tr><th>data_file_ids</th><td><ul><li>90</li><li>None</li></ul></td></tr><tr><th>data_file_name</th><td><ul><li>Patient_valid_1</li><li>None</li></ul></td></tr><tr><th>data_file_size</th><td>43203781</td></tr><tr><th>end_time</th><td>Wed, 29 Apr 2015 14:17:01 GMT</td></tr><tr><th>first_name</th><td>Zujun</td></tr><tr><th>id</th><td>276</td></tr><tr><th>last_name</th><td>Yang</td></tr><tr><th>patient_id</th><td>1212</td></tr><tr><th>patient_key</th><td>56</td></tr><tr><th>project_id</th><td>2</td></tr><tr><th>project_identifier</th><td>DEFAULT</td></tr><tr><th>project_name</th><td>DEFAULT</td></tr><tr><th>site_id</th><td>4</td></tr><tr><th>site_identifier</th><td>000</td></tr><tr><th>site_name</th><td>Test Site</td></tr><tr><th>start_time</th><td>Tue, 28 Apr 2015 13:47:03 GMT</td></tr><tr><th>status</th><td>0 out of 1</td></tr><tr><th>timestamp</th><td>Tue, 14 May 2024 18:56:35 GMT</td></tr><tr><th>timezone</th><td><ul><li>None</li></ul></td></tr><tr><th>total_monitoring_time</th><td>88197541</td></tr></table>



Multiple sort fields can be given as shown in the cell bellow which will first sort in descending order according to first_name and then sort according to data_file_size


```python
sorted_patients_1 = database_utils_obj.get_all_patients(sort=[
    # first sort in descending order according to first_name
    {
        "field": "first_name",
        "sort": "desc"
    },
    # then sort in ascending order according to data_file_size
    {
        "field": "data_file_size",
        "sort": "asc"
    },
])

# view the first patient in the sorted list
view_json(sorted_patients_1[0])
```




<table border="1"><tr><th>annotation_count</th><td>0</td></tr><tr><th>annotation_ids</th><td><ul><li>None</li></ul></td></tr><tr><th>data_file_ids</th><td><ul><li>90</li><li>None</li></ul></td></tr><tr><th>data_file_name</th><td><ul><li>Patient_valid_1</li><li>None</li></ul></td></tr><tr><th>data_file_size</th><td>43203781</td></tr><tr><th>end_time</th><td>Wed, 29 Apr 2015 14:17:01 GMT</td></tr><tr><th>first_name</th><td>Zujun</td></tr><tr><th>id</th><td>276</td></tr><tr><th>last_name</th><td>Yang</td></tr><tr><th>patient_id</th><td>1212</td></tr><tr><th>patient_key</th><td>56</td></tr><tr><th>project_id</th><td>2</td></tr><tr><th>project_identifier</th><td>DEFAULT</td></tr><tr><th>project_name</th><td>DEFAULT</td></tr><tr><th>site_id</th><td>4</td></tr><tr><th>site_identifier</th><td>000</td></tr><tr><th>site_name</th><td>Test Site</td></tr><tr><th>start_time</th><td>Tue, 28 Apr 2015 13:47:03 GMT</td></tr><tr><th>status</th><td>0 out of 1</td></tr><tr><th>timestamp</th><td>Tue, 14 May 2024 18:56:35 GMT</td></tr><tr><th>timezone</th><td><ul><li>None</li></ul></td></tr><tr><th>total_monitoring_time</th><td>88197541</td></tr></table>



Some of the other fields which can be used for sorting are: 'patient_identifier', 'site_id', 'project_id', 'data_file_size', 'annotation_count', 'start_time', 'end_time', 'total_monitoring_time', 'timestamp', 'first_name', and 'status'

<h2>Accessing Annotations</h2>

<h3>Accessing Data File Ids</h3>
To access a Patient's annotation we will require their data file ids. We can do that as shown below using the functions we have seen above


```python
most_annoations_sort = database_utils_obj.get_all_patients(sort=[
    # first sort in descending order according to annotation_count
    {
        "field": "annotation_count",
        "sort": "desc"
    }
])

# chcek which patient has the most annotations
patient_most_annotation = most_annoations_sort[0]
print(f"The patient with most annotation is {patient_most_annotation['first_name']}")

# get the data file ids for this patient
data_file_ids = patient_most_annotation["data_file_ids"]
```

    The patient with most annotation is Ethan


<h3>Accessing Annotation groups</h3>
Annotation groups can be accessed by using method `get_annotation_groups`. This method takes a data_file_id and returns annotation groups"


```python
# create an empty list to store the annotation groups
all_annotation_groups = []

for data_file_id in data_file_ids:
    # get the annotation groups for each data file id
    annotation_groups = database_utils_obj.get_annotation_groups(data_file_id)

    # add the annotation groups to the list
    all_annotation_groups.extend(annotation_groups)

print("There are", len(all_annotation_groups), "annotation groups for this patient")

# view the first annotation group
view_json(all_annotation_groups[0])
```

    5





<table border="1"><tr><th>group_name</th><td>new_new_session</td></tr><tr><th>id</th><td>45</td></tr><tr><th>nannotations</th><td>20</td></tr><tr><th>timestamp</th><td>Wed, 03 Apr 2024 15:57:56 GMT</td></tr></table>



<h3>Accessing Annotations</h3>
All annotations in an annotation group can be accessed through the method `get_annotations_by_group`. This method takes in a list of annotation group ids and will return annotations for all of the groups



```python
# create a list to store the annotation group ids
group_ids = [group['id'] for group in all_annotation_groups]

# create an empty list to store the annotations
all_annotations = []

# retrieve the annotations for all the annotation groups
annotations = database_utils_obj.get_annotations_by_group(group_ids)
print("There are", len(annotations), "annotations for this patient")

# view the first annotations
view_json(annotations)
```

    5





<table border="1"><tr><th>40</th><td></td></tr><tr><th>45</th><td><table border="1"><thead><tr><th>annotation_description</th><th>annotation_text</th><th>annotation_type</th><th>color</th><th>end_time</th><th>group_id</th><th>group_name</th><th>id</th><th>modalities</th><th>obj_name</th><th>opacity</th><th>start_time</th><th>tags</th><th>timestamp</th><th>user_email</th><th>user_full_name</th><th>user_id</th></tr></thead><tbody><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898334825</td><td>45</td><td>new_new_session</td><td>226</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898326312</td><td>None</td><td>Fri, 10 May 2024 18:45:51 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898330946</td><td>45</td><td>new_new_session</td><td>227</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898325557</td><td>None</td><td>Fri, 10 May 2024 18:45:53 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898320803</td><td>45</td><td>new_new_session</td><td>228</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898317336</td><td>None</td><td>Fri, 10 May 2024 18:45:58 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898326690</td><td>45</td><td>new_new_session</td><td>220</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898321095</td><td>None</td><td>Fri, 10 May 2024 18:44:39 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898330088</td><td>45</td><td>new_new_session</td><td>221</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898325076</td><td>None</td><td>Fri, 10 May 2024 18:44:42 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898327616</td><td>45</td><td>new_new_session</td><td>222</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898322433</td><td>None</td><td>Fri, 10 May 2024 18:44:43 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898332250</td><td>45</td><td>new_new_session</td><td>223</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898329401</td><td>None</td><td>Fri, 10 May 2024 18:44:49 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898325763</td><td>45</td><td>new_new_session</td><td>224</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898322296</td><td>None</td><td>Fri, 10 May 2024 18:45:03 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898324596</td><td>45</td><td>new_new_session</td><td>225</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898303623</td><td>None</td><td>Fri, 10 May 2024 18:45:06 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898320803</td><td>45</td><td>new_new_session</td><td>229</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898318125</td><td>None</td><td>Fri, 10 May 2024 18:45:59 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898332376</td><td>45</td><td>new_new_session</td><td>230</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898326695</td><td>None</td><td>Fri, 10 May 2024 18:46:04 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898329373</td><td>45</td><td>new_new_session</td><td>231</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898324807</td><td>None</td><td>Fri, 10 May 2024 18:46:30 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898328635</td><td>45</td><td>new_new_session</td><td>232</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898324807</td><td>None</td><td>Fri, 10 May 2024 18:46:32 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898336890</td><td>45</td><td>new_new_session</td><td>233</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898332874</td><td>None</td><td>Fri, 10 May 2024 18:47:59 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898336890</td><td>45</td><td>new_new_session</td><td>234</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898333217</td><td>None</td><td>Fri, 10 May 2024 18:48:01 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898323665</td><td>45</td><td>new_new_session</td><td>235</td><td><ul><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898308787</td><td>None</td><td>Fri, 10 May 2024 18:49:46 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898324992</td><td>45</td><td>new_new_session</td><td>236</td><td><ul><li>EEG_C3</li><li>ABP_Mean</li><li>EEG_C4</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898318164</td><td>None</td><td>Fri, 10 May 2024 18:49:56 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898317645</td><td>45</td><td>new_new_session</td><td>237</td><td><ul><li>EEG_C3</li><li>ABP_Mean</li><li>EEG_C4</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898308548</td><td>None</td><td>Fri, 10 May 2024 18:51:30 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898344319</td><td>45</td><td>new_new_session</td><td>564</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898324800</td><td></td><td>Thu, 20 Jun 2024 19:22:28 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td>Remove this data later</td><td>Artifact</td><td>My Custom Type</td><td>#f44e3b</td><td>1511898330912</td><td>45</td><td>new_new_session</td><td>634</td><td><ul><li>ABP_na</li><li>EEG_C3</li><li>ABP_Mean</li><li>EEG_C4</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.2</td><td>1511898322674</td><td><ul><li>art</li><li>artifact</li></ul></td><td>Thu, 27 Jun 2024 21:30:29 GMT</td><td>ethan@moberganalytics.com</td><td>Ethan Moyer</td><td>44</td></tr></tbody></table></td></tr><tr><th>50</th><td></td></tr><tr><th>158</th><td><table border="1"><thead><tr><th>annotation_description</th><th>annotation_text</th><th>annotation_type</th><th>color</th><th>end_time</th><th>group_id</th><th>group_name</th><th>id</th><th>modalities</th><th>obj_name</th><th>opacity</th><th>start_time</th><th>tags</th><th>timestamp</th><th>user_email</th><th>user_full_name</th><th>user_id</th></tr></thead><tbody><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898309689</td><td>158</td><td>Test Session 2</td><td>555</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898305319</td><td></td><td>Fri, 14 Jun 2024 16:58:23 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898302986</td><td>158</td><td>Test Session 2</td><td>556</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898302837</td><td></td><td>Fri, 14 Jun 2024 16:58:24 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898326837</td><td>158</td><td>Test Session 2</td><td>557</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898325467</td><td></td><td>Fri, 14 Jun 2024 16:58:24 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898349356</td><td>158</td><td>Test Session 2</td><td>558</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898340356</td><td></td><td>Fri, 14 Jun 2024 16:58:24 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898344319</td><td>158</td><td>Test Session 2</td><td>546</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898340134</td><td></td><td>Fri, 14 Jun 2024 16:58:19 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898332356</td><td>158</td><td>Test Session 2</td><td>547</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898324874</td><td></td><td>Fri, 14 Jun 2024 16:58:19 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898316060</td><td>158</td><td>Test Session 2</td><td>548</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898310689</td><td></td><td>Fri, 14 Jun 2024 16:58:20 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898360430</td><td>158</td><td>Test Session 2</td><td>549</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898353763</td><td></td><td>Fri, 14 Jun 2024 16:58:20 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898331134</td><td>158</td><td>Test Session 2</td><td>550</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898317689</td><td></td><td>Fri, 14 Jun 2024 16:58:20 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898356171</td><td>158</td><td>Test Session 2</td><td>552</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898341652</td><td></td><td>Fri, 14 Jun 2024 16:58:21 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898312578</td><td>158</td><td>Test Session 2</td><td>554</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898308023</td><td></td><td>Fri, 14 Jun 2024 16:58:22 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898331493</td><td>158</td><td>Test Session 2</td><td>559</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898319134</td><td></td><td>Fri, 14 Jun 2024 17:34:52 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898338889</td><td>158</td><td>Test Session 2</td><td>560</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898335898</td><td></td><td>Fri, 14 Jun 2024 17:34:53 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898334152</td><td>158</td><td>Test Session 2</td><td>561</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898323266</td><td></td><td>Fri, 14 Jun 2024 17:34:53 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898352767</td><td>158</td><td>Test Session 2</td><td>562</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898313211</td><td></td><td>Fri, 14 Jun 2024 17:34:54 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr><tr><td></td><td>Custom Annotation Config</td><td>My Custom Type</td><td>#76fafa</td><td>1511898344790</td><td>158</td><td>Test Session 2</td><td>563</td><td><ul><li>EEG_ECoG2</li><li>ICP_na</li><li>ABP_na</li><li>ABP_Syst</li><li>EEG_ECoG5</li><li>RR_na</li><li>EEG_ECoG1</li><li>Tperf_na</li><li>Tcore_na</li><li>UVP_na</li><li>EEG_Pz</li><li>ABP_Dias</li><li>EEG_AuxRef</li><li>EEG_P7</li><li>EEG_Fp2</li><li>EEG_P8</li><li>EEG_C4-T7</li><li>EEG_ECoG6</li><li>EEG_P4</li><li>EEG_F7</li><li>PLETH_na</li><li>PerfPPA_na</li><li>PerfK_na</li><li>EEG_Cz</li><li>NBP_Mean</li><li>EEG_Y2</li><li>EEG_C4</li><li>EEG_Fp1</li><li>EEG_Y1</li><li>perfDeltaT_na</li><li>EEG_F8</li><li>EEG_T7</li><li>EEG_T8</li><li>EEG_O2</li><li>EEG_ECoG4</li><li>HR_na</li><li>ECG_II</li><li>EEG_T7-C3</li><li>EEG_X1</li><li>EEG_X2</li><li>EEG_P3</li><li>EEG_Fz</li><li>EEG_C3-Cz</li><li>EEG_F4</li><li>EEG_ECoG3</li><li>NBP_Syst</li><li>CPP_na</li><li>EEG_C3</li><li>EEG_Ref</li><li>EEG_O1</li><li>EEG_Gnd</li><li>UVP_Mean</li><li>NBP_Dias</li><li>EEG_F3</li><li>EEG_Composite</li><li>SpO2_na</li><li>RESP_na</li><li>EEG_Cz-C4</li><li>EtCO2_na</li><li>ICP_Mean</li><li>ABP_Mean</li></ul></td><td>Patient_2017_Nov_28__19_45_15_437001</td><td>0.3</td><td>1511898340468</td><td></td><td>Fri, 14 Jun 2024 17:34:54 GMT</td><td>chenkevin711@gmail.com</td><td>Kevin Chen</td><td>110</td></tr></tbody></table></td></tr><tr><th>189</th><td></td></tr></table>



## Coming Soon: New Features in Development

We're constantly working to improve `DatabaseUtils` and expand its capabilities. Here's a sneak peek at some features we're developing:

- **Write Annotations and Algorithmic Annotation**: Soon, you'll be able to write annotations back to the database, allowing for more interactive and dynamic data analysis. This feature will enable you to automatically generate and write annotations based on algorithms or external data sources. For example, you could develop an algorithm to detect specific patterns in patient data and automatically create annotations for those patterns. These annotations would then be visible to any clinicians working with the MCP frontend, enhancing collaboration and insights.
 
- **Device and Measurement Metadata**: We're working on providing access to metadata about measurements and devices, which will enable more sophisticated queries and analyses. 

Stay tuned for these exciting additions that will enhance your ability to work with patient data and annotations!
