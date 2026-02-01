## Neuromonitoring Analytics Wiki

The **Neuromonitoring Analytics Wiki** is a curated, open, and evolving knowledge base for understanding continuous neuromonitoring data and analytics.

Its purpose is to help clinicians, researchers, and engineers **interpret neuromonitoring signals responsibly**, recognize artefacts and limitations, and understand how derived analytics are computed and used—before those analytics are applied in research or clinical workflows.

This Wiki is for educational purposes only. It does not provide treatment recommendations or replace clinical judgment.

## What this Wiki covers

The Wiki focuses on **interpretation and understanding**, including:

* Core neuromonitoring signals (e.g., ICP, ABP, EEG)
* Common artefacts and data quality issues in the neuro ICU
* Derived analytics and algorithms (e.g., PRx, CPPopt, aEEG, DSA)
* How signal quality and artefacts affect downstream metrics
* Descriptive clinical workflows and retrospective review pitfalls
* Limitations, uncertainty, and appropriate use of analytics

Content is grounded in published literature, real-world experience, and multimodal neuromonitoring practice.

## How this Wiki fits into a larger ecosystem

The Neuromonitoring Analytics Wiki is one pillar of a broader education and learning ecosystem that supports the use of advanced analytics in critical care:

* **The Wiki** — canonical interpretation and educational context
* **GitHub** — reproducible example notebooks and reference implementations
* **Discord** — community discussion, questions, and real-world edge cases

Together, these form a continuous loop:

**Discussion → Knowledge → Implementation → Discussion**

## Why this exists

Advanced neuromonitoring analytics only create value when users share a common understanding of:

* what the data represents,
* when it can be trusted,
* and when it cannot.

This Wiki exists to build that shared understanding—openly, rigorously, and with humility about uncertainty.

## Relationship to the Moberg Clinical Platform (MCP)

Much of the content in this Wiki is written with MCP in mind, since MCP aggregates multimodal neuromonitoring data and supports exploratory analytics via notebooks.

However, the concepts in this Wiki are **not limited to any single product**. Where MCP-specific behavior is discussed, it is clearly identified.

## Governance and review

* Content is **versioned** and **reviewed** over time
* Selected pages are periodically reviewed by a **Scientific Advisory Board** to ensure accuracy, balance, and alignment with current scientific understanding
* Pages clearly indicate review status and last-reviewed dates

This allows the Wiki to remain a **living resource** rather than a static manual.

## Table of contents

This repository is organized to prioritize **artefacts and data quality** first, with a stable structure that can grow over time without reorganization.

### 🟢 Welcome

* `00-welcome/00-what-this-is.md` *(TODO)*
* `00-welcome/01-how-to-use.md` *(TODO)*
* `00-welcome/02-versioning-and-review.md` *(TODO)*

### 🧠 Foundations

* `01-foundations/signals-and-sampling.md` *(TODO)*
* `01-foundations/trends-vs-waveforms.md` *(TODO)*
* `01-foundations/multimodal-context.md` *(TODO)*

### ⚠️ Artefacts and Data Quality 

* [`02-artifacts/overview.md`](02-artifacts/overview.md)
* `02-artifacts/pattern-library.md` *(TODO)*
* `02-artifacts/icp-artifacts.md` *(TODO)*
* `02-artifacts/abp-artifacts.md` *(TODO)*
* `02-artifacts/eeg-artifacts.md` *(TODO)*

### 📈 Signals

* `03-signals/icp.md` *(TODO)*
* `03-signals/abp.md` *(TODO)*
* `03-signals/cpp.md` *(TODO)*
* `03-signals/eeg.md` *(TODO)*

### 📊 Displays

* `04-displays/aeg.md` *(TODO)*
* `04-displays/dsa.md` *(TODO)*

### 🧮 Analytics and Algorithms

* `05-algorithms/prx.md` *(TODO)*
* `05-algorithms/cppopt.md` *(TODO)*

### 🔬 Clinical & Research Workflows

* `06-workflows/daily-review.md` *(TODO)*
* `06-workflows/retrospective-review.md` *(TODO)*

### 🧪 Analytics Studio 

* `07-analytics-studio/what-it-is.md` *(TODO)*
* `07-analytics-studio/using-notebooks.md` *(TODO)*
* `07-analytics-studio/prototyping-reports.md` *(TODO)*

### 📚 Reference 

* `10-glossary/glossary.md` *(TODO)*
* `10-glossary/references.md` *(TODO)*

## How to use this repository

* Browse the Markdown files directly on GitHub
* Use the Wiki as a reference when interpreting neuromonitoring data or analytics
* Link to pages from notebooks, presentations, or educational material
* Propose edits, clarifications, or new pages via pull requests

See [`CONTRIBUTING.md`](https://github.com/moberg-analytics/.github/blob/main/CONTRIBUTING.md) for contribution guidelines.
