# Invoice Processing Pipeline with Gemini Multimodal AI

## Overview

A multimodal AI pipeline that processes invoices by combining image analysis and structured text data in a single API call. Gemini 2.0 Flash extracts invoice data from a PNG image, cross-references it against three data sources (email, webhook, supplier database), validates the results, and outputs a final decision — APPROVE, REVIEW, or REJECT.

## Technologies Used

- **Python** — core language
- **Gemini 2.0 Flash (google/gemini-2.0-flash-001)** — multimodal LLM via OpenRouter API
- **OpenRouter API** — LLM backend
- **Pandas** — CSV loading and supplier data processing
- **base64** — invoice image encoding for API
- **Matplotlib** — pipeline diagram visualisation
- **Google Colab** — development environment

## Setup

### Install dependencies

```bash
pip install requests pandas matplotlib
```

### Run in Google Colab

1. Open the notebook in Google Colab
2. Add your OpenRouter API key to Colab Secrets as `OPENROUTER_API_KEY`
3. Run all cells in order

## Project Structure

The notebook runs through 7 stages:

### Stage 1 — Invoice Image Download
- Downloads a sample invoice PNG from a public URL
- Displays the image in the notebook

### Stage 2 — Data Source Simulation
Three data sources are simulated:

| Source | Format | Contents |
|---|---|---|
| Email | Python dict | Sender, subject, body, date |
| Suppliers CSV | CSV / DataFrame | Supplier name, payment days, trusted status |
| Webhook | JSON | Invoice ID, expected amount, currency, status |

### Stage 3 — Gemini Multimodal Analysis
- Invoice image encoded as base64
- All three data sources combined into a single text context
- One API call sends both image and text to Gemini
- Gemini extracts invoice fields and returns structured JSON

### Stage 4 — Validation & Error Handling
Five automated checks on Gemini's response:

| Check | Description |
|---|---|
| `supplier_found` | Supplier exists in the database and is trusted |
| `amount_match` | Invoice total matches expected webhook amount |
| `has_line_items` | At least one line item present |
| `total_positive` | Total amount is greater than zero |
| `currency_valid` | Currency is USD, EUR, $ or € |

Final decision: `APPROVE`, `REVIEW`, or `REJECT`

### Stage 5 — Results Export
- Full result saved to `rezultats.json`
- Includes all sources, Gemini analysis, validation results, and final decision

### Stage 6 — Pipeline Diagram
- Matplotlib visualisation of the full data flow
- Saved as `scenarijs.png`

### Stage 7 — Prompt Export
- Gemini prompt template saved to `prompti.txt` for reuse

## Output Files

| File | Description |
|---|---|
| `rezultats.json` | Full structured output with decision |
| `scenarijs.png` | Pipeline architecture diagram |
| `prompti.txt` | Saved Gemini prompt template |

## Why Multimodal

Neither the invoice image nor the text data alone is sufficient for full validation. Gemini combines both in one call — extracting visual data from the image (amounts, supplier name, date) and cross-referencing it against structured context (expected amounts, supplier trust status). This enables complete automated validation impossible with text or image alone.

## GDPR Considerations

All data in this project is fictional — no real personal data is used. In a real-world deployment:
- Invoice images often contain personal data (name, address) protected under GDPR
- Sending images to third-party APIs (OpenRouter) requires a Data Processing Agreement (DPA)
- Alternatives: use Gemini API via Google Cloud EU region, anonymise data before sending, or use a local multimodal model (e.g. Ollama + LLaVA)

## Key Concepts Demonstrated

- **Multimodal AI** — combining image and text input in a single LLM call
- **Invoice automation** — extracting structured data from unstructured documents
- **Multi-source data fusion** — merging email, webhook, and database context
- **Automated validation** — rule-based checks on LLM output
- **JSON output parsing** — handling and cleaning LLM-generated JSON responses
