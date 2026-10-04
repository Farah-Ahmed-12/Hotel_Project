# Hotel Customers Analytics Dashboard

An end-to-end data analytics project that turns **83K+ raw hotel booking records** into an interactive **Streamlit dashboard** and a set of **data-backed business recommendations** on cancellations, revenue drivers, and customer loyalty.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Cleaning-150458?logo=pandas&logoColor=white)
![Plotly](https://img.shields.io/badge/Plotly-Visualization-3F4F75?logo=plotly&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-Dashboard-FF4B4B?logo=streamlit&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)

![Main Dashboard](images/01-main-dashboard.png)

---

##  Project Overview

Hotels lose money through cancellations, no-shows, and over-reliance on a few booking channels. This project answers the questions a revenue manager would ask:

- Where does the revenue come from (channel, market segment, nationality)?
- Which segments and booking windows carry the highest cancellation risk?
- What factors drive total revenue per guest?
- How loyal are customers, and who are the high-value guests worth retaining?

The work covers the full pipeline: **data cleaning → feature engineering → exploratory analysis → interactive dashboard → actionable recommendations**.

---

## Key Results

| Metric | Value |
|---|---|
| Customers analysed (after cleaning) | **81,196** |
| Total revenue | **$30.26M** |
| Avg. revenue per night | **$130.3** |
| Overall cancellation rate | **0.3%** |
| Lodging share of revenue | **81.6%** ($24.69M lodging vs $5.58M other) |
| Customers with at least one booking | **76.9%** (62,430) |

---

##  Dataset

- **Source file:** `HotelCustomersDataset.xlsx`
- **Raw size:** 83,590 rows × 28 columns
- **Clean size:** 81,196 rows × 31 columns

| Group | Columns |
|---|---|
| Customer | `nationality`, `age`, `dayssincecreation`, `dayssincefirststay`, `dayssincelaststay` |
| Booking behaviour | `averageleadtime`, `bookingscanceled`, `bookingsnoshowed`, `bookingscheckedin`, `roomnights`, `personsnights` |
| Revenue | `lodgingrevenue`, `otherrevenue` |
| Channel | `distributionchannel`, `marketsegment` |
| Special requests | `srkingsizebed`, `srtwinbed`, `srquietroom`, `srhighfloor`, `srlowfloor`, `srcrib`, `sraccessibleroom`, and more |

---

##  Data Cleaning & Feature Engineering

| Step | What was done |
|---|---|
| Drop identifiers | Removed `id`, `namehash`, `docidhash` (no analytical value) |
| Invalid ages | Ages ≤ 0 or > 100 set to missing, then filled with the mean (distribution is near-symmetric, skew = -0.16) |
| Invalid lead time | Removed 10 rows with negative `averageleadtime` |
| New-customer flags | `dayssincelaststay` / `dayssincefirststay` = -1 (new customers, ~19.9K rows) replaced with 0 |
| Duplicates | Removed **2,384** duplicate rows |
| Outlier check | IQR method on every numeric column; outliers kept because they represent genuine high-value guests |
| Type optimisation | Converted `nationality`, `distributionchannel`, `marketsegment` to `category` |
| File size | Exported a gzip-compressed CSV: **8.68 MB → 1.18 MB** |

**Engineered features**

```python
df['total_revenue']     = df['lodgingrevenue'] + df['otherrevenue']
df['total_bookings']    = df['bookingscanceled'] + df['bookingsnoshowed'] + df['bookingscheckedin']
df['lodging revenue per room night'] = df['lodgingrevenue'] / df['roomnights'].replace(0, np.nan)
```

---

##  Dashboard Walkthrough

The Streamlit app has **3 pages**. A global sidebar filter (Nationality, Distribution Channel, Market Segment, Age Range) updates every chart and KPI.

### 1️ Main Dashboard
Dataset preview plus four headline KPIs: Total Customers, Total Revenue, Avg Revenue/Night, and Cancellation Rate.

<p align="center">
  <img width="1920" height="930" alt="Screenshot (2173)" src="https://github.com/user-attachments/assets/d56fb79a-c1d0-4494-9674-2b1fb27543d6" />
</p>

### 2️ Analysis (4 tabs)

####  Booking Overview
Booking status mix, market segment distribution, and booking vs no-booking engagement.

<table>
  <tr>
    <td><img src="images/03-booking-overview.png" alt="Booking overview"></td>
    <td><img src="images/04-customer-engagement.png" alt="Customer engagement"></td>
  </tr>
</table>

####  Cancellation Analysis
Cancellations by lead time, revenue vs cancellation rate per segment (dual axis), and conversion / cancel / no-show rates per channel.

<table>
  <tr>
    <td><img src="images/05-cancellation-lead-time.png" alt="Cancellations vs lead time"></td>
    <td><img src="images/06-revenue-vs-cancellation.png" alt="Revenue vs cancellation by segment"></td>
  </tr>
  <tr>
    <td colspan="2"><img src="images/07-conversion-by-channel.png" alt="Conversion by channel"></td>
  </tr>
</table>

####  Revenue Analysis
Revenue sources, revenue by channel and segment, room nights vs revenue (OLS trendline),correlation-based revenue factors, and revenue by customer cohort.

<table>
  <tr>
    <td><img src="images/08-revenue-analysis.png" alt="Revenue sources and channel"></td>
    <td><img src="images/09-revenue-by-segment.png" alt="Revenue by segment"></td>
  </tr>
  <tr>
    <td><img src="images/10-revenue-drivers.png" alt="What affects total revenue"></td>
    <td><img src="images/11-revenue-by-cohort.png" alt="Revenue by customer cohort"></td>
  </tr>
</table>

####  Customer Insights
Special room requests and a world map of customer nationalities.

<table>
  <tr>
    <td><img src="images/12-special-room-requests.png" alt="Special room requests"></td>
    <td><img src="images/13-nationality-map.png" alt="Customer distribution by nationality"></td>
  </tr>
</table>

### 3️ Insights & Recommendations
Two tabs that turn the analysis into decisions: **Market Segment Analysis** and **Customer Value & Risk**. Each card follows an *Insight → Analysis → Recommendation* structure.

<table>
  <tr>
    <td><img src="images/14-insights-market-segment.png" alt="Financial performance insights"></td>
    <td><img src="images/15-insights-booking-behavior.png" alt="Booking behavior and risk"></td>
  </tr>
  <tr>
    <td><img src="images/16-insights-loyalty.png" alt="Loyalty and operations"></td>
    <td><img src="images/17-insights-customer-value.png" alt="Customer value and risk"></td>
  </tr>
</table>

---

##  Key Findings

-  **Lodging drives ~82% of revenue**; most transactions are small, so ancillary services are an under-used upside.
-  **Aviation** has the highest revenue per guest (~600) but also the highest cancellation rate (**5.1%**).
-  **Direct** bookings are the safest channel: **0.3%** cancellations with solid revenue (~400).
-  Cancellations are highest for **last-minute bookings (0–30 days lead time)** and fall steadily as lead time grows.
-  **Complementary** (free) rooms bring low revenue and a **3.2%** cancellation rate.
-  Most guests and agents book **only once**, a clear loyalty gap.
-  **New customers (0–30 days)** generate very little revenue compared with customers who joined 1–12 months ago or more.
-  **King-size bed** is by far the most requested room preference, followed by twin bed and quiet room.
-  The guest base is concentrated in **Europe**.
-  **Groups** and **Travel Agents** show strong profitability with very low cancellation rates, a stable base for the hotel.

---

##  Strategic Recommendations

| Area | Insight | Recommendation |
|---|---|---|
| **Lodging revenue** | Main income driver, but mostly low-value transactions | Upsell extra services during the stay to lift total revenue |
| **Aviation segment** | High revenue, 5.1% cancellation rate | Strict cancellation policy or higher deposits for airline crews |
| **Last-minute bookings** | Highest cancellation risk, customers easily switch hotels | Require pre-payment for bookings made 48 hours before check-in |
| **Direct channel** | "Golden customers": loyal, reliable, 0.3% cancellation | Increase direct-booking marketing budget to cut third-party costs |
| **Customer loyalty** | Most customers and agents book only once | Launch a loyalty programme with a second-stay discount |
| **Complementary rooms** | Low revenue and 3.2% cancellation (opportunity cost) | Review the free-room policy and reduce it in busy seasons |
| **VIP guests** | Some guests spend 9,000+ from only 2–3 bookings | Identify them and offer luxury packages to secure return stays |
| **One-time visitors** | Most clients sit in the low-booking, low-revenue area | Retention campaign, e.g. discount on the 2nd stay |
| **High-volume accounts** | Accounts with 20+ bookings cancel more often | Volume-based cancellation policy with non-refundable deposits |
| **Economy frequent travelers** | Guests booking 30–40 times at low-cost rates | Upselling strategy (spa, breakfast) to raise their contribution |

---

##  Tech Stack

- **Python**: core language
- **Pandas / NumPy**: cleaning, aggregation, feature engineering
- **Plotly Express & Graph Objects**: interactive charts (donut, dual-axis, scatter with OLS trendline, choropleth map)
- **Streamlit**: multi-page interactive dashboard with sidebar filters

---

##  Project Structure

```
hotel-analytics-dashboard/
├── Hotel_Analysis_Complete.ipynb   # Cleaning, EDA, feature engineering
├── app.py                          # Streamlit dashboard
├── cleaned_hotel_data.csv.gz       # Cleaned dataset used by the app
├── requirements.txt
└── README.md
```

---

> `statsmodels` is required for the OLS trendline, and `openpyxl` is only needed to re-run the notebook on the original Excel file.
