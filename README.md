# Combination Therapy Agent from Clinical Trials

**Author:** Alexandra McKillip  
**Date:** January 2026  
**Methodology:** ClinicalTrials.gov API v2, LLM-Augmented Entity Extraction, Data Visualization  
**Data Source:** [ClinicalTrials.gov](https://clinicaltrials.gov/data-api/api)

## Project Overview

Combination therapies play a critical role in modern medicine, where pairing complementary drugs can enhance efficacy or improve patient outcomes. Many drugs are evaluated not only as monotherapies but also alongside complementary drugs in clinical trials, making combination patterns an important source of insight.

This project implements an AI-driven analysis pipeline that identifies combination therapy trends from publicly available interventional clinical trial data from ClinicalTrials.gov. Starting from a user-defined seed drug, the system:

- fetches a target number of clinical trials in which the seed drug is an intervention
- identifies and cleans co-administered partner drugs
- ranks partner drugs by frequency of co-occurrence
- generates a histogram summarizing combination trends

Rather than attempting to exhaustively analyze all registered trials, this project is designed to capture high-level trends efficiently, while remaining extensible for more comprehensive analysis.

## Methodology

### 1. API v2 Data Retrieval
The system interfaces with the **ClinicalTrials.gov API v2**, handling the modern, heavily nested JSON structures. It drills into the `protocolSection` to extract specific intervention modules, ensuring higher data fidelity than simple keyword scraping.

### 2. LLM-Augmented Extraction
Unlike traditional keyword-based filtering, this agent uses **Gemini 2.0 Flash** to:
* **Identify** therapeutic partners while ignoring noise (placebos, devices, or general procedures).
* **Normalize** heterogeneous names into standardized generic scientific names.
* **Contextualize** pairings to ensure they are true co-administrations.

### 3. Engineering for API Resilience
To handle the limitations of AI services (specifically API rate limits), the pipeline incorporates professional-grade engineering patterns:
* **Batch Processing:** Bundles multiple trials into single LLM prompts to optimize token usage and request frequency.
* **Rate Limiting:** Implements standardized "cool-down" periods (`time.sleep`) to respect API Quotas (RPM/RPD).
* **Mock Resilience Layer:** A failover mechanism that allows the pipeline to validate downstream visualization logic even when external API limits are reached.


## Script Descriptions

| Script | Description |
| :--- | :--- |
| **fetch_trials.py** | Helper function to fetch clinical trial data from ClinicalTrials.gov API for a specified drug. |
| **run_fetch_trials.py** | Core utility for paginated requests to the ClinicalTrials.gov API v2. |
| **extract_partners.py** | Orchestrates LLM batches, handles API errors, and performs drug frequency aggregation. |
| **run_pipeline.py** | Executes the end-to-end flow from raw query to final PNG chart. |

## Pipeline Outputs

Example outputs are provided in the `data/` directory for the seed drug Paclitaxel.

![Paclitaxel Partner Drugs Histogram](data/top_partner_drugs_histogram.png)

| File | Description |
|----|----|
| `raw_trials.json` | Filtered clinical trials for the seed drug |
| `partner_drugs.json` | Trial-level partner drug lists |
| `partner_frequencies.json` | Aggregated partner drug frequency counts |
| `top_partner_drugs.png` | Histogram of most frequent combination partners |


## Usage
### 1. Environment Requirements

This project is developed using **Python 3.9+**.

Required Libraries:
- External libraries: `requests`, `matplotlib`, `google-genai`
- Standard libraries: `json`, `collections`, `subprocess`, `os`, `sys`, `re`

### 2. Gemini API Configuration

The agent utilizes the **Gemini 2.0 Flash** model for intelligent drug extraction. To use the live AI features, you must provide a valid API key from Google AI Studio.

Steps to Configure:

1. Obtain an API Key:
   Generate a free key at https://ai.google/studio

2. Set Environment Variable:
   For security, the script retrieves your key from the system environment. Run the following in your terminal:

```bash
export GEMINI_API_KEY="your_api_key_here"
```

### 3. Running the Pipeline

From the project root directory, run:

```bash
python scripts/run_pipeline.py <SEED_DRUG>
```

Replace <SEED_DRUG> with the name of the drug you want to use as the starting point for your analysis.

