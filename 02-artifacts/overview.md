Title: Artifacts in Continuous Neuromonitoring \
Applies to: MCP, ICM+, CNS Monitor \
Status: Draft \
Last reviewed: 1 Feb 2026 \
Reviewed by: Ethan Jacob Moyer \
Audience: Clinician | Researcher | Engineer

# Artifacts in Continuous Neuromonitoring

Artifacts are unavoidable in continuous neuromonitoring. They arise from routine clinical care, patient physiology, technical limitations of sensors, and environmental influences. When unrecognized, artifacts can obscure true physiology, mimic pathology, and distort derived analytics.

This page provides a **practical, signal-centric framework** for understanding where artifacts come from, how they present, and why they matter—before discussing any specific algorithm or metric.

This content is **educational**, not prescriptive, and does not replace clinical judgment.

## What is an artifact?

In continuous neuromonitoring, an *artifact* is any distortion or irregularity in a recorded signal that **does not reflect the underlying physiological state** of the patient.

A key principle is that artifacts are defined **from the perspective of the signal**, not intent or cause. Whether a disturbance originates from a clinical procedure, sensor interface, or environmental interference, it becomes clinically relevant once it alters the recorded data stream.

This signal-centric definition aligns with current consensus in the neuromonitoring literature [1].

## Why artifacts are unavoidable

Modern neurocritical care relies on high-resolution, continuous signals such as:
- intracranial pressure (ICP)
- arterial blood pressure (ABP)
- electroencephalography (EEG)
- brain oxygenation (PbtO₂, rSO₂)

These signals are:
- acquired at the bedside,
- often invasive or semi-invasive,
- and collected during dynamic clinical care.

Even with meticulous calibration and experienced clinical teams, **artifact-free data do not exist** in real-world neuromonitoring [2].

## Primary sources of artifacts

Artifacts in continuous neuromonitoring are commonly grouped into four overlapping categories:

### 1. Clinical procedural activities

Routine care frequently introduces transient signal disturbances, including:
- patient repositioning
- airway suctioning
- probe manipulation
- arterial line flushing
- EVD opening/closing and calibration

These events can generate short-lived spikes, baseline shifts, or waveform distortions that are **physiologically implausible**, yet visually convincing.

Importantly, many of these artifacts are obvious *in real time* but difficult to identify retrospectively without annotations or waveform-level context.

### 2. Patient-related physiology

Patient physiology itself can introduce artifactual signal changes through:
- voluntary or involuntary movement
- shivering or myoclonus
- sweating, bleeding, or edema
- inflammatory responses around invasive sensors
- clotting or air bubbles in fluid-filled systems

For example, gradual arterial line damping due to clot formation can attenuate pulsatility and bias downstream analyses, even though values may remain within “acceptable” numeric ranges.

### 3. Technical equipment properties

Although modern medical devices are highly regulated, technical artifacts still occur due to:
- sensor drift over time
- degradation of invasive probes
- saturation at physiological extremes
- software or data-integration errors
- sampling or timestamp distortions

These artifacts are often **persistent rather than transient**, making them especially problematic for long-term trend analysis and retrospective review.

### 4. Environmental factors

External influences can introduce artifacts without direct patient interaction, including:
- electromagnetic interference from nearby equipment
- ambient light affecting optical sensors
- mechanical vibration from beds or ventilators
- thermal fluctuations

These artifacts may be subtle and modality-specific, but they can compound with other artifact sources to degrade signal quality.

## Artifacts rarely occur in isolation

In practice, artifacts often result from **interacting mechanisms**.

For example:
- repeated probe handling (procedural) may exacerbate clot formation (physiological),
- leading to waveform damping that worsens over time (technical),
- while patient movement adds superimposed noise (environmental).

This interplay makes artifacts **non-stationary**, difficult to model, and highly context dependent.

## How artifacts present in data

Artifacts can be broadly described by their temporal footprint:

### Transient artifacts
- brief spikes or drops
- abrupt waveform distortions
- typically seconds in duration

Often caused by suctioning, flushing, or movement.

### Contextual artifacts
- abnormal morphology persisting minutes to hours
- altered pulsatility or spectral content
- patterns that may mimic pathology

Examples include arterial line damping or motion-related EEG artifacts.

### Persistent artifacts
- long-term baseline drift
- gradual loss of signal fidelity
- subtle but cumulative bias

These are especially dangerous for trend-based metrics and dose calculations.

## Why artifacts matter for interpretation and analytics

Artifacts affect neuromonitoring at multiple levels:

- **False positives**: artifacts mimicking pathological events  
- **False negatives**: true events masked by noise  
- **Metric distortion**: biasing derived indices such as autoregulation metrics  
- **Prognostic error**: inflating or suppressing cumulative exposure metrics  

Multiple studies have shown that unfiltered artifacts can significantly alter interpretations of intracranial hypertension, cerebral perfusion, and cerebrovascular reactivity.

These effects become more pronounced as monitoring shifts toward:
- higher temporal resolution,
- multimodal integration,
- and automated or AI-assisted analytics.

## Key takeaways

- Artifacts are **inevitable** in continuous neuromonitoring  
- They arise from clinical care, patient physiology, technology, and environment  
- Artifacts often interact and compound over time  
- Misinterpreting artifacts can meaningfully distort clinical and research conclusions  
- Understanding artifacts is a **prerequisite** for interpreting any derived metric or advanced analytic

## References

1. Chen X. et al. *Artifacts in continuous neuromonitoring*. **Nature Reviews Bioengineering** (2025). 
2. Foreman B. et al. Challenges and opportunities in multimodal monitoring and data analytics in traumatic brain injury. *Curr Neurol Neurosci Rep* (2021).  
3. Kirkness C.J. et al. Intracranial pressure waveform analysis: clinical and research implications. *J Neurosci Nurs* (2000).  
4. Howells T. et al. Effects of ventricular drainage on the intracranial pressure signal and pressure reactivity index. *J Clin Monit Comput* (2017).
