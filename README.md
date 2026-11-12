# RFM & K-Means Customer Segmentation — Thesis

**Turning customer purchasing behavior into targeted marketing recommendations.**

An undergraduate thesis project by **Cordirianto Alvian**, BINUS University, exploring RFM analysis, K-Means clustering, and AI-assisted marketing reporting.

**Thesis title:** *Assessing RFM Analysis with K-Means Clustering to Provide Automated AI-Generated Marketing Strategy*

## Project overview

Customer data is useful only when it supports a clear decision. This project explores how customers can be grouped by purchasing behavior and how those groups can inform marketing strategies.

The research combines **Recency, Frequency, and Monetary (RFM) analysis** with **K-Means clustering**. An n8n automation prototype coordinates data preparation, analytical calculations, AI-written report sections, and PDF report delivery.

## Business questions

- Which customers have similar purchasing behavior?
- How do recency, purchase frequency, and product spending distinguish customer groups?
- What marketing actions could support retention, reactivation, and repeat purchases for each group?
- Which parts of the analysis and reporting process can be automated?

## Dataset

**Customer Personality Analysis**, Kaggle, 2021, attributed in the thesis to **M. Thakkar**.

**[View and download the dataset on Kaggle](https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis)**

The dataset contains 2,240 customer records, including customer characteristics, purchase counts, spending across product categories, campaign responses, and customer recency.

The raw dataset is not bundled with this repository. Download it from the original source and review the dataset page for its current terms and license.

### Dataset citation

> M. Thakkar, “Customer personality analysis,” Kaggle, 2021. [Online]. Available: https://www.kaggle.com/datasets/imakash3011/customer-personality-analysis. [Accessed: Oct. 2025].

The URL above restores the separator in `customer-personality-analysis` from the line-wrapped citation supplied with the thesis.

## RFM features in the supplied workflow

| Feature | Meaning | Current implementation |
| --- | --- | --- |
| Recency | Time since the customer's latest purchase | Uses the dataset's `Recency` field. |
| Frequency | Purchase-frequency measure | Sums `NumDealsPurchases`, `NumWebPurchases`, `NumCatalogPurchases`, and `NumStorePurchases`. See implementation notes below. |
| Monetary | Spending on the selected product category | Uses the selected `Mnt...` field, such as `MntWines`. |

Product options include wines, fruits, meat, fish, sweets, and gold products. The workflow retains customers with positive spending in the selected category and filters `Dt_Customer` within the requested date range.

## Analytical approach

1. Select a customer enrollment-date range and product category.
2. Read the customer dataset from Google Sheets and construct RFM features.
3. Calculate descriptive statistics and identify outliers using the interquartile range (IQR).
4. Prepare standardized RFM features and explore candidate cluster counts using the elbow method.
5. Interpret customer segments and develop segment-specific marketing recommendations.
6. Compile the analysis into a structured report.

These steps describe the research approach. The exported V2 workflow is a development prototype; its implementation boundaries are documented below.

## Automation workflow

**[Download the sanitized n8n workflow JSON](workflows/thesis-rfm-kmeans-v2.json)**

The supplied n8n V2 prototype contains 40 nodes. Its design includes:

- A Gmail trigger and request parser for a date range and product category.
- Google Sheets integration for source data and report sections.
- JavaScript Code nodes for RFM preparation, summary statistics, IQR outlier handling, feature scaling, and elbow-method calculations.
- K-Means WCSS calculations for candidate cluster counts, using a seeded implementation.
- OpenAI agents and structured output parsers for report drafting.
- HTML report assembly, APITemplate.io PDF generation, and a Gmail reply step.

The public workflow export retains the original analytical code, prompts, and connections. Credential references, account-specific Google Sheets locations, sender addresses, execution metadata, and pinned data have been removed. The template is inactive and requires your own configuration before use. Connected Sheets, API credentials, the raw dataset, and generated reports are not included.

## Tools and technologies

| Area | Tools / methods |
| --- | --- |
| Customer analytics | RFM analysis, descriptive statistics, IQR |
| Clustering | K-Means, within-cluster sum of squares (WCSS), elbow method |
| Automation | n8n, JavaScript Code nodes |
| Data and report storage | Google Sheets |
| Report drafting | OpenAI models, structured output parsing |
| Report delivery | APITemplate.io, HTML/PDF, Gmail |

## Setup

1. Download the dataset from the Kaggle link above and import it into a Google Sheet, retaining its original column names.
2. Import `workflows/thesis-rfm-kmeans-v2.json` into n8n.
3. Connect your own Gmail, Google Sheets, OpenAI, and APITemplate.io credentials.
4. Select your dataset spreadsheet in **Pull from Dataset**. Configure a report sheet for the other Google Sheets nodes and review their column mappings.
5. Create the report-sheet columns: `Report Name`, `Chapter 1` through `Chapter 5`, and `Chapter 1 Content` through `Chapter 5 Content`.
6. Replace the placeholder sender filter in **Gmail Trigger** with your own authorized sender. Review the configured model choices and PDF service settings.
7. Review the implementation notes below and manually test each analytical branch before activating the trigger. Test PDF generation and email delivery using your own account.

The existing parser expects a request such as:

```text
Analyze from 01-01-2012 to 31-12-2014
product Wines
```

Use separate lines for the date range and product. Confirm how dates are parsed before running an analysis.

## Implementation notes

The repository preserves the supplied V2 prototype rather than presenting it as a fully validated analytical pipeline:

- **Date interpretation:** `Dt_Customer` represents customer enrollment, so the date filter selects an enrollment cohort rather than transactions within a sales period. The current JavaScript date parser also requires review for unambiguous day-month-year handling.
- **Frequency definition:** deal purchases may overlap purchases recorded by channel. The supplied sum should be validated before being interpreted as a count of unique purchases.
- **Scaling:** the Feature Scaling node performs z-score standardization. The Chapter 2 prompt currently describes Min-Max scaling; the prompt should be aligned with the calculation.
- **Elbow calculation:** the supplied elbow node uses raw RFM fields. A consistent distance-based analysis should apply the chosen scaling to the elbow and final clustering calculations.
- **Final cluster assignments:** the export computes WCSS for candidate K values, but it does not expose a dedicated deterministic output of final customer cluster labels and centroids. The Chapter 4 prompt references clustering results without receiving that complete output. Add and validate that step before treating generated cluster descriptions or recommendations as analytical findings.
- **Report validation:** verify branch merging, Sheets mappings, charts in the PDF, and AI-written statements against calculated outputs. This repository does not claim reproduced cluster results, execution-time benchmarks, or a completed end-to-end run.

## Repository contents

| File | Purpose |
| --- | --- |
| `README.md` | Project overview, dataset citation, setup, and implementation notes |
| `workflows/thesis-rfm-kmeans-v2.json` | Sanitized n8n V2 workflow export |
| `.gitignore` | Excludes local credentials, raw data, and generated outputs |

## Research reference

[Thesis research materials on Zenodo](https://doi.org/10.5281/zenodo.21308327)

## Author

**Cordirianto Alvian**  
BINUS University — Information Systems & Management  
[Portfolio](https://cordiriantoalvian.github.io/cordirianto-alvian-portfolio/)
