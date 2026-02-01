Title: Neurocritical Care Measurements \
Applies to: MCP, ICM+, CNS Monitor \
Status: Draft \
Last reviewed: 1 Feb 2026 \
Reviewed by: Ethan Jacob Moyer \
Audience: Clinician | Researcher | Engineer

# Neurocritical Care Measurements

This page provides standardized, educational descriptions of common **measurements used in neurocritical care monitoring**.  
These definitions are intended to support interpretation, analytics development, and data harmonization.

This content is educational, not prescriptive, and does not replace clinical judgment.

## Intracranial Pressure (ICP)

**Description**  
Pressure within the cranial vault resulting from brain tissue, cerebrospinal fluid, and intracranial blood volume.

**Units**  
mmHg

**Measurement Method**  
Invasive probes (ventricular, parenchymal, epidural, or subdural).

**Clinical Context**  
Used to detect intracranial hypertension and assess risk of secondary brain injury.

**Common Considerations**  
Probe drift, reference leveling, waveform damping, and tissue effects.

## Mean Arterial Pressure (MAP)

**Description**  
Time-averaged arterial pressure representing systemic perfusion pressure.

**Units**  
mmHg

**Measurement Method**  
Invasive arterial catheter or non-invasive cuff.

**Clinical Context**  
Key determinant of cerebral perfusion pressure.

**Common Considerations**  
Transducer leveling, damping, and averaging windows.

## Systolic Arterial Pressure (SAP)

**Description**  
Peak arterial pressure during cardiac systole.

**Units**  
mmHg

**Measurement Method**  
Arterial catheter or cuff.

## Diastolic Arterial Pressure (DAP)

**Description**  
Minimum arterial pressure during cardiac diastole.

**Units**  
mmHg

**Measurement Method**  
Arterial catheter or cuff.

## Cerebral Perfusion Pressure (CPP)

**Description**  
Calculated pressure gradient driving cerebral blood flow.

**Formula**  
CPP = MAP − ICP

**Units**  
mmHg

**Clinical Context**  
Used to assess adequacy of cerebral perfusion.

**Common Considerations**  
Synchronization of MAP and ICP, correct leveling.

## Heart Rate (HR)

**Description**  
Number of cardiac cycles per minute.

**Units**  
beats per minute (bpm)

**Measurement Method**  
ECG or pulse waveform.

## Oxygen Saturation (SpO₂)

**Description**  
Peripheral arterial oxygen saturation.

**Units**  
%

**Measurement Method**  
Pulse oximetry.

**Common Considerations**  
Motion artefact, poor perfusion, nail polish.

## End-Tidal Carbon Dioxide (EtCO₂)

**Description**  
Partial pressure of CO₂ at the end of expiration.

**Units**  
mmHg

**Measurement Method**  
Capnography.

**Clinical Context**  
Surrogate for ventilation status and CO₂ control.

## Body Temperature

**Description**  
Core or peripheral body temperature.

**Units**  
°C or °F

**Measurement Method**  
Bladder, esophageal, rectal, or surface sensors.

## Brain Tissue Oxygen Tension (PbtO₂)

**Description**  
Partial pressure of oxygen in brain tissue.

**Units**  
mmHg

**Measurement Method**  
Invasive brain tissue probes.

**Clinical Context**  
Detection of cerebral hypoxia.

**Common Considerations**  
Probe location sensitivity, calibration drift.

## Regional Cerebral Oxygen Saturation (rSO₂)

**Description**  
Regional cerebral oxygen saturation measured by near-infrared spectroscopy.

**Units**  
%

**Measurement Method**  
Non-invasive optical sensors.

**Common Considerations**  
Extracranial contamination, sensor placement.

## Electroencephalography (EEG)

**Description**  
Electrical activity of the brain recorded from scalp or intracranial electrodes.

**Units**  
µV

**Measurement Method**  
Surface or invasive electrodes.

**Clinical Context**  
Seizure detection, background assessment, sedation monitoring.

## Amplitude-Integrated EEG (aEEG)

**Description**  
Time-compressed EEG display emphasizing amplitude trends.

**Units**  
µV (displayed logarithmically)

**Derived From**  
Raw EEG.

## Density Spectral Array (DSA)

**Description**  
Time-frequency representation of EEG power.

**Units**  
Power (arbitrary or normalized units)

**Derived From**  
Spectral decomposition of EEG.

## Cerebral Blood Flow Velocity (CBFV)

**Description**  
Velocity of blood flow in major cerebral arteries.

**Units**  
cm/s

**Measurement Method**  
Transcranial Doppler ultrasound.

**Common Considerations**  
Probe angle, insonation window variability.
