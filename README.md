# ⚡ PJM Insight Flow — Energy Forecasting Pipeline

**End-to-end time series forecasting system for PJM West Region hourly energy consumption, with automated alerting and an interactive dashboard.**

Built as part of the [MAICEN](https://www.e-zigurat.com/en/master/artificial-intelligence-construction/) MSc program (Module 5, Unit 1) at Zigurat Institute of Technology.

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    PJM Insight Flow                         │
├──────────────┬──────────────────┬───────────────────────────┤
│  📓 Jupyter  │  ⚙️ n8n Pipeline │  📊 Lovable Dashboard     │
│  Notebook    │                  │                           │
│              │  Webhook Trigger │  Interactive web app       │
│  - Cleaning  │       ↓          │  for non-technical users  │
│  - EDA       │  Clean & Analyze │                           │
│  - Prophet   │       ↓          │  - Upload CSV             │
│  - SARIMA    │  Alert Check     │  - Multi-scale viz        │
│  - LSTM      │     ↙    ↘       │  - Seasonality charts     │
│              │  ⚠️ Alert  ✅ OK  │  - ACF/PACF explorer      │
│              │     ↘    ↙       │  - Forecast comparison    │
│              │  Gmail Report    │  - Report download        │
└──────────────┴──────────────────┴───────────────────────────┘
```

---

## 📂 Repository Structure

```
pjm-insight-flow/
├── README.md
├── M5U1_Group_1_Assignment_PJMW_Final.ipynb   # Full analysis notebook
├── Energy_Forecasting_Alert_Pipeline.json      # n8n workflow (cleaned)
├── M5U1_Presentation.pdf                       # Slide deck (13 slides)
├── LICENSE
└── screenshots/                                # (optional) demo images
```

---

## 📓 Notebook — What's Inside

The Jupyter notebook performs a complete time series analysis on **~145,000 hourly energy readings** from the PJM West interconnection:

| Exercise | Topic | Key Techniques |
|----------|-------|----------------|
| 1 | Data Cleaning | Deduplication, frequency enforcement (`asfreq`), linear interpolation |
| 2 | Multi-Scale Visualization | Daily (24h), weekly, and full-history plots |
| 3 | Seasonality Analysis | Hourly and day-of-week consumption patterns |
| 4 | ACF / PACF + Stationarity | Augmented Dickey-Fuller test, differencing |
| 5 | Prophet Forecasting | Additive vs. multiplicative seasonality, 52-week horizon |
| 6 (Bonus) | SARIMA Forecasting | SARIMA(1,1,1)(1,1,1,52) with confidence intervals |
| 7 (Bonus) | LSTM Deep Learning | 64-32 unit architecture, 52-week lookback |

**Four models compared on a 52-week test horizon:**

| Model | Type |
|-------|------|
| Prophet V1 (additive) | Decomposition |
| Prophet V2 (multiplicative) | Decomposition |
| SARIMA(1,1,1)(1,1,1,52) | Statistical |
| LSTM (64-32 units) | Deep Learning |

---

## ⚙️ n8n Automation Pipeline

The `Energy_Forecasting_Alert_Pipeline.json` transforms the notebook analysis into a **production-ready automated alerting system**:

**Workflow:**

1. **Webhook** receives forecast data (POST) — from the notebook or a scheduled job
2. **Code node** cleans and summarizes: calculates 4-week rolling average, year-over-year change, and alert thresholds (90% / 95% of historical max)
3. **IF node** routes based on alert level:
   - **⚠️ CRITICAL / WARNING** → styled HTML alert email via Gmail
   - **✅ NORMAL** → weekly status report email via Gmail
4. **Webhook Response** returns JSON summary to the caller

**Alert Levels:**
- `NORMAL` — consumption within safe operating range
- `WARNING` — average exceeds 90% of historical maximum
- `CRITICAL` — average exceeds 95% of historical maximum

---

## 📊 Interactive Dashboard

A companion web app built with [Lovable](https://lovable.dev/) provides an accessible interface for non-technical stakeholders:

🔗 **Live App:** [pjm-insight-flow.lovable.app](https://pjm-insight-flow.lovable.app)

Features: CSV upload, multi-scale visualization, seasonality charts, ACF/PACF explorer, forecast comparison, and downloadable reports.

---

## 🚀 Quick Start

### 1. Run the Notebook

Open in Google Colab (recommended) or locally:

```bash
pip install pandas numpy matplotlib seaborn statsmodels prophet tensorflow scikit-learn
```

**Dataset:** Download `PJMW_hourly.csv` from [Kaggle — Hourly Energy Consumption](https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption) and place it in the notebook directory.

### 2. Import the n8n Workflow

1. Open your [n8n](https://n8n.io/) instance
2. Go to **Workflows → Import from File**
3. Select `Energy_Forecasting_Alert_Pipeline.json`
4. Update the Gmail credentials with your own OAuth2 connection
5. Replace the default recipient email in the Code node
6. Activate the workflow

### 3. Test the Pipeline

Send a POST request to the webhook with summary data:

```json
{
  "email": "your-email@example.com",
  "summary": {
    "mean_mw": 3200,
    "max_mw": 9594,
    "min_mw": 1200,
    "total_rows": 145366,
    "date_range": "2002-2018"
  }
}
```

---

## 🤖 Claude AI Integration

The n8n workflow is connected to **Claude via the MCP (Model Context Protocol) connector**, enabling natural language triggers for the entire pipeline:

```
User asks Claude → Claude triggers n8n webhook → n8n processes data → Gmail delivers report
```

A user can simply say *"Send an energy report"* in Claude, and the full pipeline executes — zero code, zero manual steps. This demonstrates how AI assistants can orchestrate backend automation workflows through conversational interfaces.

---

## 🛠️ Tech Stack

| Layer | Technology |
|-------|------------|
| Analysis | Python, Pandas, NumPy, Matplotlib, Seaborn |
| Forecasting | Prophet, SARIMA (statsmodels), LSTM (TensorFlow/Keras) |
| Statistical Testing | Augmented Dickey-Fuller, ACF/PACF |
| Automation | n8n (webhook + code + Gmail nodes) |
| Dashboard | Lovable (React web app) |
| Alerting | Gmail API via n8n OAuth2 |
| AI Orchestration | Claude + n8n MCP connector |

---

## 🎓 Context

This project was developed for **Module 5, Unit 1 (Time Series and Practical Applications)** of the MSc in Artificial Intelligence for Architecture, Engineering, Construction & Operations (MAICEN) at Zigurat Institute of Technology.

The goal was to go beyond academic analysis and demonstrate how time series forecasting can be deployed as a **continuous decision-support system** in AECO operations — relevant to energy management in mega-projects like Diriyah, NEOM, and other GCC developments.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).
