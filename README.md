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
    <img width="1904" height="857" alt="Screenshot (2174)" src="https://github.com/user-attachments/assets/1e41e67c-5c20-402f-83b1-018a21abf778" />

    <img width="1920" height="867" alt="Screenshot (2175)" src="https://github.com/user-attachments/assets/c9a58590-94a6-4741-bbee-1961f06ab351" />

  </tr>
</table>

####  Cancellation Analysis
Cancellations by lead time, revenue vs cancellation rate per segment (dual axis), and conversion / cancel / no-show rates per channel.

<table>
  <tr>
    <img width="1920" height="877" alt="Screenshot (2176)" src="https://github.com/user-attachments/assets/deb1b624-e448-410d-9f22-47157fc93509" />

    <img width="1920" height="837" alt="Screenshot (2177)" src="https://github.com/user-attachments/assets/b3066e5e-46eb-4189-b74e-4a66bb101ef8" />

  </tr>
  <tr>
   <img width="1920" height="856" alt="Screenshot (2178)" src="https://github.com/user-attachments/assets/4410dcf2-801e-4df4-bc0d-6eb0030f9f32" />

  </tr>
</table>

####  Revenue Analysis
Revenue sources, revenue by channel and segment, room nights vs revenue (OLS trendline),correlation-based revenue factors, and revenue by customer cohort.

<table>
  <tr>
    <img width="1905" height="854" alt="Screenshot (2179)" src="https://github.com/user-attachments/assets/4ae34719-d002-4bb2-941e-4bad851135b5" />

    <img width="1920" height="811" alt="Screenshot (2180)" src="https://github.com/user-attachments/assets/54bc0679-f5aa-4cab-8a47-c34ffd6891b4" />

    <img width="1556" height="743" alt="Screenshot (2181)" src="https://github.com/user-attachments/assets/53402375-b771-4562-b58a-be0504c3d65e" />


  </tr>
  <tr>
    <img width="1913" height="710" alt="Screenshot (2182)" src="https://github.com/user-attachments/assets/60960b08-c059-473a-b766-de32d648f290" />

    <img width="1913" height="681" alt="Screenshot (2183)" src="https://github.com/user-attachments/assets/94606592-c54d-4969-9a27-9129f854aa84" />

  </tr>
</table>

####  Customer Insights
Special room requests and a world map of customer nationalities.

<table>
  <tr>
<img width="1504" height="838" alt="Screenshot (2184)" src="https://github.com/user-attachments/assets/843119c3-f450-4b47-b75b-722fa512c3d5" />

<img width="1516" height="545" alt="Screenshot (2185)" src="https://github.com/user-attachments/assets/c81cd665-7c45-4d1a-a6ef-ca8a49eaa467" />

  </tr>
</table>

### 3️ Insights & Recommendations
Two tabs that turn the analysis into decisions: **Market Segment Analysis** and **Customer Value & Risk**. Each card follows an *Insight → Analysis → Recommendation* structure.

<table>
  <tr>
    <img width="1914" height="757" alt="Screenshot (2186)" src="https://github.com/user-attachments/assets/678978ea-dda1-495f-8045-45669736c7e4" />

    <img width="1905" height="862" alt="Screenshot (2187)" src="https://github.com/user-attachments/assets/17aeba52-7928-432e-b985-05a5873c34b9" />

  </tr>
  <tr>
    <img width="1920" height="695" alt="Screenshot (2188)" src="https://github.com/user-attachments/assets/a80232a0-de99-4b4c-a4c3-e6976e6a3554" />

   <img width="1920" height="787" alt="Screenshot (2189)" src="https://github.com/user-attachments/assets/825cb98b-8135-43bf-bbcd-a0f71b8466bc" />

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
