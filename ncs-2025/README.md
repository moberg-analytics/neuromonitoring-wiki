# The 2025 Informatics, Big Data, and AI Workshop
NCS 23rd Annual Meeting \
September 18, 2025 in Montréal, Québec.

## Jupyterhub in Moberg Cloud Platform

**For more information or help, please email support@moberganalytics.com**

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

We've prepared the following exercises for this workshop, each corresponds to a different notebook in this folder.

1. [**Patient Metadata and Data Harmonization**](./01-patient-metadata-access-and-harmonization.md): learn how to navigate the TRACK-TBI dataset, create measurement availability matrices across patients, extract temporal metadata, and load/visualize physiological signals to understand multi-site ICU data heterogeneity.

2. [**Data Formats, Conversion, and Plotting**](./02-data-formats-conversion-and-plotting.md): understand why ICU monitoring data is converted to HDF5 format for efficient analysis, learn to convert cns monitor archives to HDF5 files, explore the hierarchical structure of exported data (trends vs waveforms), and practice loading/plotting physiological signals with proper time alignment and metadata handling.

*Note: Topic 3 does not have a corresponding notebook.*

4. [**Cerebral Autoregulation Analysis**](./04-cerebral-autoregulation-analysis.md): learn to calculate the pressure reactivity index (PRx) as a measure of cerebral autoregulation, derive CPPopt estimates for optimal cerebral perfusion pressure, apply artifact reduction techniques to physiological signals, and explore extensions including autoregulation limits and non-invasive NIRS-based approaches (COx, MAPopt).

5. [**Data Modelling and Visualization**](./05-data-modelling-and-visualization.md): explore machine learning applications on neuromonitoring data including pretrained models for artifact detection in ABP/ECG waveforms, LSTM-based ICP forecasting, and patient outcome prediction using case-based reasoning vs XGBoost approaches.

**Click on the link to open the appropriate notebook once you've chosen an exercise.**
