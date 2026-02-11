# Domain Glossary

## Primary Measurements

| Abbreviation | Full Name | Units | Description | Measurement Method |
|---|---|---|---|---|
| ICP | Intracranial Pressure | mmHg | Pressure within the cranial vault from brain tissue, CSF, and blood volume | Invasive probes (ventricular, parenchymal, epidural, subdural) |
| ABP | Arterial Blood Pressure | mmHg | Continuous arterial pressure waveform | Invasive arterial catheter or non-invasive cuff |
| MAP | Mean Arterial Pressure | mmHg | Time-averaged arterial pressure representing systemic perfusion | Derived from ABP waveform or reported by monitor |
| SAP | Systolic Arterial Pressure | mmHg | Peak arterial pressure during cardiac systole | Arterial catheter or cuff |
| DAP | Diastolic Arterial Pressure | mmHg | Minimum arterial pressure during cardiac diastole | Arterial catheter or cuff |
| HR | Heart Rate | bpm | Number of cardiac cycles per minute | ECG or pulse waveform |
| SpO2 | Peripheral Oxygen Saturation | % | Arterial oxygen saturation measured peripherally | Pulse oximetry |
| rSO2 / StO2 | Regional Cerebral Oxygen Saturation | % | Cerebral tissue oxygen saturation | Near-infrared spectroscopy (NIRS) — non-invasive optical sensors |
| PbtO2 | Brain Tissue Oxygen Tension | mmHg | Partial pressure of oxygen in brain tissue parenchyma | Invasive brain tissue probes |
| EtCO2 | End-Tidal Carbon Dioxide | mmHg | Partial pressure of CO2 at end of expiration — surrogate for ventilation status | Capnography |
| Temp | Body Temperature | C or F | Core or peripheral body temperature | Bladder, esophageal, rectal, or surface sensors |
| EEG | Electroencephalography | uV | Electrical activity of the brain | Surface scalp electrodes or intracranial electrodes |
| CBFV | Cerebral Blood Flow Velocity | cm/s | Blood flow velocity in major cerebral arteries | Transcranial Doppler ultrasound |
| ECG | Electrocardiography | mV | Electrical activity of the heart | Surface electrodes |

## Derived Displays

| Abbreviation | Full Name | Units | Derived From |
|---|---|---|---|
| aEEG | Amplitude-Integrated EEG | uV (log scale) | Time-compressed EEG emphasizing amplitude trends |
| DSA | Density Spectral Array | Power (uV^2/Hz) | Time-frequency spectral decomposition of EEG |

## Derived Indices

| Abbreviation | Full Name | Units | Formula / Method |
|---|---|---|---|
| CPP | Cerebral Perfusion Pressure | mmHg | CPP = MAP - ICP |
| PRx | Pressure Reactivity Index | dimensionless [-1, 1] | Rolling Pearson correlation of ABP and ICP (5-min window, 1-min step) |
| COx | Cerebral Oximetry Index | dimensionless [-1, 1] | Rolling Pearson correlation of ABP and mean bilateral rSO2 |
| CPPOpt | Optimal Cerebral Perfusion Pressure | mmHg | CPP value that minimizes PRx via binned polynomial fit over 4h window |
| MAPOpt | Optimal Mean Arterial Pressure | mmHg | MAP value that minimizes COx via binned polynomial fit over 4h window |
| LLA | Lower Limit of Autoregulation | mmHg | CPP/MAP below which PRx/COx exceeds threshold (autoregulation impaired) |
| ULA | Upper Limit of Autoregulation | mmHg | CPP/MAP above which PRx/COx exceeds threshold (autoregulation impaired) |

## Clinical Concepts

**Cerebral Autoregulation**
The brain's ability to maintain relatively constant cerebral blood flow despite changes in systemic blood pressure. Quantified via PRx (pressure-based) and COx (oxygenation-based). Intact autoregulation: PRx/COx near or below 0. Impaired: PRx/COx > 0.25.

**Cerebral Perfusion Pressure (CPP) Optimization**
Clinical strategy of targeting a patient's CPPOpt — the CPP at which autoregulation is best preserved. CPPOpt is found by plotting PRx against CPP bins and fitting a U-shaped curve; the minimum is the optimal value.

**Spreading Depolarization (SD)**
A slowly propagating wave of near-complete neuronal depolarization followed by sustained suppression of brain activity. Detected in EEG via characteristic depression spreading across channels. Occurs in traumatic brain injury (TBI), subarachnoid hemorrhage (SAH), and ischemic stroke. Associated with secondary brain injury and worse outcomes.

## Common Device / Modality Naming Variations

ICP signals may appear under different names depending on the probe type and bedside monitor:
- `ICP` — generic intracranial pressure
- `IC1`, `IC2` — numbered ICP channels
- `ICT` — ICP from temperature-sensing probe
- `EVD` — external ventricular drain (also measures ICP)
- `IPM` — intraparenchymal monitor

ABP signals vary by source:
- `ABP` — generic arterial blood pressure
- `ART` — arterial line
- `Ao` — aortic pressure
- `P1`, `P2` — numbered pressure channels

rSO2/StO2 signals:
- `rSO2` — regional cerebral oxygen saturation
- `StO2` — tissue oxygen saturation (same measurement, different vendor naming)
- Typically labeled with `Left`/`Right` location for bilateral monitoring
