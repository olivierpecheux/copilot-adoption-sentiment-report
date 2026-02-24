<p align="center">
  <img src="docs/images/logo.png" alt="M365 Copilot Analytics" width="120"/>
</p>

<h1 align="center">M365 Copilot — Adoption & Sentiment Report</h1>

<p align="center">
  A Power BI report that combines <strong>Microsoft 365 Copilot usage data</strong> with <strong>employee survey sentiment</strong> to give you a complete picture of Copilot adoption across your organization.
</p>

<p align="center">
  <a href="#-quick-start">Quick Start</a> •
  <a href="#-data-sources">Data Sources</a> •
  <a href="#-report-pages">Report Pages</a> •
  <a href="#-setup-guide">Setup Guide</a> •
  <a href="#-faq">FAQ</a>
</p>

---

## 📊 Report Pages

### 📈 Adoption Overview

Track Copilot usage trends across your organization — active users, weekly actions, and usage distribution by department and user segment.

<!-- TODO: Add screenshot -->
![Adoption Overview](docs/images/adoption-overview.png)

### 📝 Sentiment Analysis

Correlate survey scores (satisfaction, productivity, readiness) with actual Copilot usage. See how sentiment differs across usage tiers and organizational units.

<!-- TODO: Add screenshot -->
![Sentiment Analysis](docs/images/sentiment-analysis.png)

### 🗣️ Comments Analysis

Explore free-text survey responses — use cases, barriers to adoption, and how employees repurpose saved time. Powered by AI-generated narrative summaries.

<!-- TODO: Add screenshot -->
![Comments Analysis](docs/images/comments-analysis.png)

### ⌛ Saved Time Analysis

Compare self-reported time savings against actual usage patterns. Break down by usage rank to understand the ROI story across user segments.

<!-- TODO: Add screenshot -->
![Saved Time Analysis](docs/images/saved-time-analysis.png)

---

## 🚀 Quick Start

1. **Download** or clone this repository
2. Open `M365 Copilot - Adoption & Sentiment.pbip` in **Power BI Desktop** (February 2025 or later)
3. Set the three file parameters (see [Setup Guide](#-setup-guide))
4. Click **Refresh**

---

## 📁 Data Sources

This report uses **3 CSV files** configured via Power BI parameters:

| Parameter | Source | Required | Description |
|---|---|---|---|
| **Raw Survey Data** | Microsoft Forms / survey tool | Optional | Survey responses with an `Email` column (UPN) plus Likert-scale scores and free-text comments |
| **Copilot Activity (MAC)** | Microsoft Admin Center | ✅ Required | Copilot usage export from **Reports > Usage > M365 Copilot** |
| **User Details (Entra)** | Microsoft Entra admin center | Optional | User list export adding display names and organization (companyName) |

### How to export each file

<details>
<summary><strong>Copilot Activity (MAC)</strong> — Microsoft Admin Center</summary>

1. Go to [admin.microsoft.com](https://admin.microsoft.com) → **Reports** → **Usage** → **Microsoft 365 Copilot**
2. Select the **30-day** report period
3. Click **Export** (top right) to download the CSV
4. The file will be named something like `CopilotActivityUserDetail_YYYY-MM-DD.csv`

> **Important:** To get real user names (not hashed), go to **Settings** → **Org settings** → **Reports** and uncheck *"Display concealed user, group, and site names in all reports"*.

</details>

<details>
<summary><strong>User Details (Entra)</strong> — Microsoft Entra admin center</summary>

1. Go to [entra.microsoft.com](https://entra.microsoft.com) → **Users** → **All users**
2. Click **Download users** → **Download**
3. The export includes `userPrincipalName`, `displayName`, and `companyName`

> The `companyName` field is used as the Organization attribute. If it's empty in your tenant, you can manually add a column or use a different Entra attribute.

</details>

<details>
<summary><strong>Raw Survey Data</strong> — Microsoft Forms or any survey tool</summary>

Export your survey responses as CSV. The file must include:

| Column | Type | Description |
|---|---|---|
| `Email` | Text | User Principal Name (must match the Copilot Activity UPNs) |
| `SurveyDate` | Date | When the response was submitted |
| `Overall satisfaction` | Integer (1-5) | Overall satisfaction with Copilot |
| `Helps me work faster` | Integer (1-5) | Speed perception |
| `Improves work quality` | Integer (1-5) | Quality perception |
| `Improves productivity` | Integer (1-5) | Productivity perception |
| `Benefits my role` | Integer (1-5) | Role relevance |
| `Copilot readiness` | Integer (1-5) | Self-assessed readiness |
| `Agents usage` | Integer (1-5) | Copilot agents familiarity |
| `Time saved using Copilot` | Text | e.g. "About 15 minutes per day" |
| `Time repurposed` | Text | How saved time is used |
| `Use cases` | Text | Most valuable use cases |
| `Barriers to adoption` | Text | Adoption blockers |
| `What else?` | Text | Additional comments |

> The `Email` column is automatically detected and renamed to `PersonId` internally for the join with Copilot Activity data.

</details>

---

## 🔧 Setup Guide

### Prerequisites

- **Power BI Desktop** — February 2025 release or later (PBIP format support required)
- **Copilot Activity data** — At least one monthly export from Microsoft Admin Center
- **Survey data** (optional) — CSV with the schema described above

### Step-by-step

1. **Clone or download** this repository

2. **Open the project** — Double-click `M365 Copilot - Adoption & Sentiment.pbip`

3. **Configure parameters** — In Power BI Desktop, go to **Home** → **Transform data** → **Edit parameters**:

   <!-- TODO: Add screenshot of parameter dialog -->
   ![Parameters](docs/images/parameters.png)

   | Parameter | What to enter |
   |---|---|
   | Raw Survey Data | Full file path to your survey CSV (or `/` to skip) |
   | Copilot Activity (MAC) | Full file path to the Admin Center export CSV |
   | User Details (Entra) | Full file path to the Entra user export CSV (or `/` to skip) |

   > ⚠️ Do **not** wrap paths in quotes. Use the full path, e.g. `C:\Data\CopilotActivity.csv`

4. **Refresh the data** — Click **Refresh** on the Home ribbon

5. **Explore the report** — Navigate through the 4 tabs

### Loading multiple months

To track adoption trends over time, **export the Copilot Activity report monthly** from the Admin Center and append/combine the CSVs. The report will automatically detect multiple `Report Refresh Date` values and show month-over-month trends.

---

## 🏗️ Project Structure

```
M365 Copilot - Adoption & Sentiment.pbip
M365 Copilot - Adoption & Sentiment.Report/
  └── definition/
      └── pages/              ← Report page definitions (4 pages)
M365 Copilot - Adoption & Sentiment.SemanticModel/
  └── definition/
      ├── expressions.tmdl    ← Parameters (3 file paths)
      ├── model.tmdl          ← Model configuration
      ├── relationships.tmdl  ← Table relationships
      └── tables/
          ├── Table.tmdl                    ← Main fact table (Copilot Activity + Entra join)
          ├── Table - Survey Sentiment.tmdl ← Survey data table
          ├── Calendar.tmdl                 ← Date dimension
          └── ...                           ← Toggle tables, slicers, helper tables
```

---

## 🔗 Key Relationships

```
Table.PersonId ─────────── Table - Survey Sentiment.PersonId
Table.Date ─────────────── Calendar.Date
```

- **PersonId** contains the User Principal Name (email), lowercased for consistent matching
- The join between usage and survey data happens on this key
- Organization data comes from the Entra export (`companyName` → `Organization`)

---

## 📐 Key Measures

| Measure | Description |
|---|---|
| `Number of Active Users` | Users with at least 1 Copilot action |
| `% Active Copilot Users` | Active / Enabled users ratio |
| `Average Weekly Actions` | Mean Copilot prompts per user per period |
| `Average Weekly Active Days` | Mean active days per user |
| `Usage Rank` | Quintile classification (Top 10%, 75-90%, 50-75%, 25-50%, Bottom 25%) |
| `Survey - Metric Value` | Dynamic survey score based on selected question |
| `Self-reported Daily Time Saved` | Average from survey time-saved responses |
| `Favorability (% of 4 or 5 Scores)` | Net positive sentiment rate |

---

## ❓ FAQ

<details>
<summary><strong>The Admin Center export shows hashed user names — how do I fix this?</strong></summary>

Go to **Microsoft 365 Admin Center** → **Settings** → **Org settings** → **Reports** and uncheck *"Display concealed user, group, and site names in all reports"*. Wait 48 hours, then re-export.

</details>

<details>
<summary><strong>Organization column is empty — what's wrong?</strong></summary>

The Organization data comes from the `companyName` field in the Entra export. If this field is not populated in your tenant, you can either:
- Populate it in Entra ID (Azure AD)
- Manually add an Organization column to the Entra CSV
- Use a different data source for org mapping

</details>

<details>
<summary><strong>Survey data isn't joining with usage data</strong></summary>

Ensure the `Email` column in your survey CSV contains the same UPN format as the Admin Center export (e.g. `user@contoso.com`). The report automatically lowercases and trims both sides for matching.

</details>

<details>
<summary><strong>Can I use this with Viva Insights data instead?</strong></summary>

This version of the report is designed for Admin Center (MAC) exports. For Viva Insights (Copilot Dashboard or custom person query), see the original report which supports both CSV and DirectQuery modes.

</details>

<details>
<summary><strong>Feature-level breakdowns show 0 — is that expected?</strong></summary>

Yes. The Admin Center export only provides total prompts and active days — it does not include per-feature action breakdowns (e.g. "Draft Word document", "Summarize email"). These measures exist for backward compatibility but will return 0 with MAC data. For feature-level detail, use Viva Insights.

</details>

---

## 📦 Sample Data

The `Sample data/` folder includes example files you can use to test the report:

| File | Description |
|---|---|
| `MAC - Copilot Activity - export.csv` | 50 users × 6 months of Copilot activity |
| `Copilot Survey - raw data (email).csv` | 50 survey responses correlated with usage |
| `Entra - Users list - export.csv` | User directory with departments |

---

## 📝 License

This project is provided as-is for internal analytics purposes.

---

<p align="center">
  Built with ❤️ for Copilot adoption teams
</p>
