# INTS Neuromonitoring Workshop - ICM+ and Moberg Analytics

## Jupyterhub in Moberg Cloud Platform

### Introduction

JupyterHub is the most efficient way to serve Jupyter notebooks to multiple users. It manages a separate Jupyter environment for each user.

JupyterHub offers a fully functional JupyterLab environment that requires no setup, providing a seamless experience for users of Moberg Clinical Platform. It comes pre-installed with a wide array of popular Python packages for data science, as well as proprietary tools specifically designed for neuromonitoring data analysis. This comprehensive suite of tools makes it an ideal platform for researchers and data scientists alike.

With single sign-on enabled, users of Moberg Cloud platform are able to log into JupyterHub seamlessly.

The JupyterHub in Moberg Cloud Platform provides users with persistent storage options. Each user has a dedicated home directory that retains their work between sessions. Additionally, the platform offers shared storage space accessible to all users, sourced from the Moberg Cloud Platform. These features ensure data persistence and facilitate collaboration among users.

### Supported Modules and Packages

The JupyterHub environment includes popular packages for data analysis in Python such as `numpy`, `pandas`, `scipy`, and `sklearn` among others. Users also have access to proprietary tools we've developed for:

- **Interacting with Data in CNS Format** (`cns_utils`) - This package enables users to read raw Moberg CNS archives with built-in error handling and file integrity checks.
- **Digital Signal Processing** (`moberg_dsp`) - This package provides users with tools for preprocessing and analyzing patient data, as well as implementations of algorithms published in recent research papers. 
- **Retrieving Patient Metadata and Annotations** (`database_utils`) - This package allows users to retrieve patient metadata from our clinical database. It also enables programmatic access to any annotations created via Moberg Cloud Platform. 
- **Machine Learning** (`sam_tools`) - This package provides users with implementations of various machine learning algorithms, as well as helper functions for training and evaluating such models.


## Workshop Exercises

We've prepared the following exercises for this workshop, each corresponds to a different notebook in the `notebooks/` folder.

0. [**`DatabaseUtils` Tutorial**](./00-DatabaseUtils-Tutorial.md): Introduces the DatabaseUtils class for accessing patient data and annotations from the Moberg Database. Covers initialization, querying patients, and retrieving annotations.

1. [**ICMH5 to CNS Conversion**](./01-ICMH5-to-CNS-conversion.md): Demonstrates how to convert data from ICM+ HDF5 output format to Moberg CNS format using our `cns_utils` library.

2. [**ABP Artifact Detection**](./02-ABP-artifact-detection.md): Evaluates different algorithms for detecting artifacts in Arterial Blood Pressure (ABP) waveforms.

3. [**ICP Forecasting**](./03-ICP-Forecasting.md): Creates a forecasting algorithm for Intracranial Pressure (ICP) by training a LSTM (Long Short Term Memory) neural network. Covers data preprocessing, model training, and performance assessment.

4. [**Predicting Outcomes with CBR**](./04-predicting-outcomes-with-CBR.md): Implements a Case-Based Reasoning (CBR) algorithm to predict intermediate endpoints for Traumatic Brain Injury (TBI) patients, comparing it to traditional machine learning approaches.

5. [**BOOST II Multi-Patient Animation**](./05-boost2-1.md): Creates a time-lapse animation using BOOST II ICP and PbtO2 data to visualize the impact of PbtO2 monitoring on brain hypoxia levels across multiple patients.

6. [**Autoregulation using CPPOpt**](./06-Autoregulation-using-CPPOpt.md): Demonstrates how to calculate optimal Cerebral Perfusion Pressure (CPPOpt) and the limits of cerebral autoregulation using the moberg_dsp library.

**Click on the link to open the appropriate notebook once you've chosen an exercise.**

## Support

For more information or if you have any questions, please email support@moberganalytics.com

