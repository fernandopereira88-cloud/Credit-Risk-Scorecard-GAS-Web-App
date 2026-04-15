# Credit Risk Scorecard
> A Google Apps Script Web Application · Project

---

## 🔗 Important Links
| | |
|---|---|
| **App** | *(paste deployed web app URL here)* |
| **Database & Codebase** | *(paste Google Sheet link here)* |
| **Vibe Coding Sessions** | Prompt Engineering · Development, implementing, and testing |

---

## Table of Contents
1. [Overview](#1-overview)
2. [Architecture](#2-architecture)
3. [Setup (Step by Step)](#3-setup-step-by-step)
4. [Sharing Options](#4-sharing-options)
5. [Customising the Scoring Model](#5-customising-the-scoring-model)
6. [AI() Requirement](#6-ai-requirement)
7. [No API Keys Required](#7-no-api-keys-required)
8. [File Reference](#8-file-reference)

---

## 1. Overview

The Credit Risk Scorecard is a fully functional, publicly shareable web application built on Google Apps Script and Google Sheets. It enables commercial operations risk teams to:

- 📋 Submit structured risk cases via a guided intake form
- ⚙️ Score each case automatically using a configurable weighted model *(deterministic, no AI required)*
- 🤖 Explain each rating using the built-in Google Sheets `AI()` function *(Gemini-powered, no API key needed)*
- 📊 Analyse portfolio risk through a live dashboard with trend charts, geography breakdowns, and segment analytics

Designed for zero-configuration sharing — deploy once, share the URL with anyone. **No Google sign-in required** by the end user.

---

## 2. Architecture

### Scoring Model

Risk is calculated deterministically using a **6-factor weighted model**:

| Factor | Default Weight | Logic |
|---|---|---|
| Transaction Size | 20% | USD-equivalent thresholds from <$10K to >$5M |
| Geography | 25% | Country mapped to FATF risk tier (1–4) via `Lookup_Countries` sheet |
| Customer Segment | 10% | Government < B2C < B2B |
| Alert Type | 25% | 10 alert categories with pre-assigned severity scores |
| Urgency | 10% | Agent-assessed Low / Medium / High |
| Prior Incidents | 10% | Count of previous cases for the same Customer ID |

Weights are stored in the `ScoringWeights` sheet and read dynamically — the Risk team can tune them **without touching any code**.

### AI Explanation (`AI()` function)

After scoring, the app writes a `=AI(...)` formula into a temporary cell in the Cases sheet (column T), flushes the spreadsheet, polls for a result (up to 16 seconds), reads the value, then clears the cell. This uses the **native Gemini integration in Google Workspace** — no API key, no external service.

If `AI()` times out or returns an error, a deterministic fallback explanation is generated automatically by identifying the top two weighted-contributing factors. The fallback is seamless — no error is shown to the user.

### Sheets-as-Database

Google Sheets serves as the backend database across three sheets:

| Sheet | Purpose |
|---|---|
| `Cases` | Primary audit-ready record of all submissions (columns A–S) |
| `ScoringWeights` | Editable model configuration |
| `Lookup_Countries` | 75+ countries mapped to FATF risk tiers |

---

## 3. Setup (Step by Step)

### a. Copy the Google Sheet
Go to **File → Make a copy**. This gives you your own editable copy tied to your Google account.

### b. Open Apps Script
Inside your copied sheet, go to **Extensions → Apps Script**.

### c. Run `initializeSheets()` once
In the Apps Script editor:
1. Open the `Code.gs` file
2. Select `initializeSheets` from the function dropdown at the top
3. Click **Run**
4. Approve any permissions prompted *(the script needs read/write access to your spreadsheet)*

You should see a toast: *"Setup complete. Ready to deploy."*
This creates all three sheets with correct headers and pre-populates country and weight data.

### d. Deploy as a Web App
1. Click **Deploy → New deployment**
2. Click the ⚙️ gear icon next to Type → select **Web app**
3. Configure as follows:
   - **Description:** `Credit Risk Scorecard v1`
   - **Execute as:** Me
   - **Who has access:** Anyone
4. Click **Deploy** and copy the Web App URL

### e. Share
Paste the web app URL into any browser — no Google sign-in required. Share it freely.

---

## 4. Sharing Options

| Option | Use Case |
|---|---|
| **Web App URL** | Share with reviewers, interviewers, or ops team for live testing |
| **Sheet copy link** | Share with the Risk team to adjust weights, inspect raw data, and review the model |

To share the Sheet: **File → Share → Copy link** *(set to Viewer or Editor as appropriate).*

---

## 5. Customising the Scoring Model

Open the `ScoringWeights` sheet and edit column B to adjust weights.

> ⚠️ **The weights must sum to 100.** The sheet includes a live `=SUM(B2:B7)` formula in the notes column so you can verify at a glance.

Changes take effect **immediately** on the next submission — no redeployment needed. The dashboard will also reflect updated weight labels automatically.

---

## 6. AI() Requirement

The AI explanation feature uses the `AI()` function built into Google Sheets, powered by Gemini and included in most Google Workspace plans.

**Requirements:**
- A Google Workspace account *(Business Starter, Standard, Plus, or Enterprise)*
- Gemini features enabled for your Workspace domain *(typically on by default for paid plans)*
- Script executing as the account owner *(set during deployment as "Execute as: Me")*

If `AI()` is unavailable *(e.g. personal Google accounts, or Workspace domains with Gemini disabled)*, the app falls back automatically to a deterministic explanation naming the top two contributing factors. No error is shown to the user.

> **To verify:** Type `=AI("Hello")` into any cell in your Sheet. If it returns a response, the feature is active.

---

## 7. No API Keys Required

This application requires **no external API keys, credentials, or third-party services** of any kind.

| Component | How it works |
|---|---|
| Risk scoring | Runs entirely in Google Apps Script |
| AI explanation | Native `AI()` Sheets function (Google Workspace entitlement) |
| Database | Google Sheets itself |
| Charts | [Chart.js](https://www.chartjs.org/) via public jsDelivr CDN |
| Fonts | Inter via Google Fonts (public CDN) |

When deployed with *"Execute as: Me"* and *"Who has access: Anyone"*, the web app is fully public and functional for any visitor — **no authentication required**.

---

## 8. File Reference

| File | Purpose |
|---|---|
| `Code.gs` | All server-side logic: scoring model, AI explanation, Sheets I/O, dashboard aggregation |
| `Index.html` | Single-file frontend: intake form, live score preview, result panel, full dashboard with charts |
| `loadSyntheticData.gs` | Utility script to seed 120 realistic demo cases into the Cases sheet |
| `README.md` | This file |

---

*Vibe Coded by **Fernando Pereira** using Claude Chat*  
*Airwallex Risk Operations Interview Project · Google Apps Script + Sheets AI()*
