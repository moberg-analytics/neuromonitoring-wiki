# Advanced Neuromonitoring in the Age of AI <br> Neuroinformatics and AI in the NeuroICU
2025 International Conference on Spreading Depolarizations (ICSD) \
December 8, 2025 in Old and Royal Windsor, UK.

This workshop provides a hands-on introduction to modern neuroinformatics workflows and AI-driven analysis for critically ill patients. Participants will learn how to load and harmonize high-frequency multimodal ICU data, detect key physiologic events, extract mechanistic biomarkers of brain health, and apply state-of-the-art machine learning models to real patient recordings. Using tools from pycns and the Moberg Clinical Platform, we will walk through the full pipeline—from raw signals to interpretable insights—focusing on spreading depolarizations, cerebral compliance, autoregulation, and predictive analytics. By the end of the session, attendees will understand how advanced data science and AI methods can augment clinical decision-making, improve monitoring accuracy, and generate new opportunities for research in the NeuroICU.

## MCP Analytics Studio

**For more information or help, please email support@moberganalytics.com**

### Introduction

The MCP Analytics Studio provides a ready-to-use workspace where clinicians, researchers, and data teams can explore high-frequency physiologic data without needing to install or configure anything. The environment includes commonly used tools for data analysis along with Moberg’s specialized neuromonitoring libraries, making it easy to review signals, analyze trends, and run clinical or research workflows directly from the Moberg Clinical Platform.

With single sign-on, users simply log into the Moberg Clinical Platform and immediately access the Analytics Studio—no extra accounts or setup required.

Each user has their own secure storage area that automatically saves their work, while a shared workspace allows teams to exchange files, notebooks, and results. This combination of personal and collaborative storage makes it simple to build analyses, revisit past work, and collaborate across clinical and research teams.

### Supported Modules and Packages

The JupyterHub environment includes popular packages for data analysis in Python such as `numpy`, `pandas`, `scipy`, and `sklearn` among others. Users also have access to proprietary tools we've developed for:

- **Interacting with Data in CNS Format** (`cns_utils`) - This package enables users to read raw Moberg CNS archives with built-in error handling and file integrity checks.
- **Digital Signal Processing** (`moberg_dsp`) - This package provides users with tools for preprocessing and analyzing patient data, as well as implementations of algorithms published in recent research papers. 
- **Retrieving Patient Metadata and Annotations** (`database_utils`) - This package allows users to retrieve patient metadata from our clinical database. It also enables programmatic access to any annotations created via Moberg Cloud Platform. 
- **Machine Learning** (`sam_tools`) - This package provides users with implementations of various machine learning algorithms, as well as helper functions for training and evaluating such models.


## Workshop Exercises

We’ve prepared the following exercises for this workshop. Each corresponds to a different notebook in this folder and is designed to take you from raw multimodal ICU data through advanced analysis of spreading depolarizations and downstream AI applications.

1. [**Data Handling**](./01-data-handling.md): Learn how to load, explore, and validate high-frequency physiologic data using pycns. You will navigate patient records, examine synchronized multi-signal time series (HR, ABP, ICP, ECG, respiratory signals) captured in the patient archive. This section introduces the foundational tools needed to work with large-scale neurocritical care datasets.

2. [**Event Detection**](./02-event-detection.md): Implement and evaluate classical physiologic event detectors to support multimodal interpretation. You will detect ECG R-peaks, respiratory cycles, and threshold-based events. You will review detected events in ECG and respiratory second-to-second waveforms in addition to detected events in long-term trends over many days.

4. [**Feature Engineering**](./03-feature-engineering.md): Transform raw signals into clinically meaningful biomarkers. You will compute cerebral compliance metrics from ICP and waveform morphology, and perform cerebral autoregulation analysis using techniques such as PRx-style correlation windows. This section focuses on deriving quantitative features that support mechanistic interpretation of brain physiology before, during, and after spreading depolarizations.

5. [**Clinical AI Applications**](./04-clinical-ai-applications.md): Explore modern machine-learning tools for automating neuromonitoring analysis. You will apply pretrained models for ABP/ECG waveform cleaning, perform short-term ICP forecasting using sequence models, and experiment with approaches for patient outcome prediction from multimodal physiologic features. This section highlights how AI can augment expert interpretation by scaling analysis across long, noisy ICU recordings.

**Click on the link to open the appropriate notebook once you've chosen an exercise.**
