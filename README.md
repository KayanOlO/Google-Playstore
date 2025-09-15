

# 📱 Google Play Store — App Performance Analytics

## 📌 Project Overview

This project analyzes **Google Play Store app data** to explore app quality, user ratings, installs, and performance categories.
The workflow combines **Python (for cleaning & preprocessing)** and **Power BI (for modeling & dashboarding)** to build insights that can help identify **Star Performers, underperforming apps, and opportunities for growth**.

## 🔍 Objectives

* Clean and preprocess raw Google Play Store dataset.
* Create **calculated columns & measures** in Power BI for installs, size, performance categories, and update recency.
* Build an **interactive dashboard** to visualize installs, ratings, categories, and performance segmentation.
* Identify **insights & recommendations** for app developers and product managers.

## 🗂 Dataset

* **Raw dataset:** `googleplaystore.csv`
* **Cleaned dataset (used in Power BI):** `googleplaystore_cleaned.csv`
* **Rows:** \~10.8K apps
* **Columns:** 13 (App, Category, Rating, Reviews, Size, Installs, Type, Price, Content Rating, Genres, Last Updated, Current Ver, Android Ver)

## ⚙️ Data Cleaning (Python)

Key cleaning steps performed in Python (`Googleplaystoredata_1757914663.pdf` notebook):

* Converted `Installs` → numeric (`10,000+` → `10000`).
* Converted `Price` → float (`$2.99` → `2.99`).
* Converted `Size` → KB (e.g., `19M` → `19000`).
* Filled missing `Rating` with **median**.
* Dropped rows with critical missing values.
* Exported cleaned dataset → `googleplaystore_cleaned.csv`.

## 📊 Power BI Modeling

### 🔹 Calculated Columns

```DAX
Install_Count_Numeric = 
SWITCH(
    TRUE(),
    SEARCH("M", [Installs], 1, 0) > 0, VALUE(SUBSTITUTE([Installs], "M", "")) * 1000000,
    SEARCH("K", [Installs], 1, 0) > 0, VALUE(SUBSTITUTE([Installs], "K", "")) * 1000,
    VALUE([Installs])
)

Performance_Category = 
SWITCH(
    TRUE(),
    [Rating] >= 4.5 && [Install_Count_Numeric] >= 1000000, "Star Performers",
    [Rating] >= 4.5 && [Install_Count_Numeric] < 1000000, "High Quality",
    [Rating] < 4.0 && [Install_Count_Numeric] >= 1000000, "Popular but Poor",
    "Needs Improvement"
)
```

### 🔹 Measures

```DAX
Avg_Rating = AVERAGE('Apps'[Rating])
Total_Installs = SUM('Apps'[Install_Count_Numeric])
Apps_Count = COUNT('Apps'[App])
Top_Performers = CALCULATE(
    COUNT('Apps'[App]),
    'Apps'[Rating] >= 4.5,
    'Apps'[Install_Count_Numeric] >= 1000000
)
Market_Share_Top = 
DIVIDE(
    CALCULATE(SUM('Apps'[Install_Count_Numeric]), 'Apps'[Performance_Category] = "Star Performers"),
    SUM('Apps'[Install_Count_Numeric])
)
```

## 📈 Dashboard Highlights

The **Power BI Dashboard**  includes:

* **KPIs:** Total Apps, Total Installs, Avg Rating, Avg Days Since Update.
* **Performance Segmentation:** Star Performers, High Quality, Popular but Poor, Needs Improvement.
* **Visuals:**

  * Rating Distribution
  * Installs vs App Size
  * Installs by Category & Content Rating
  * Market Share of Top Performers
  * Top Apps (Installs & Ratings)

## 💡 Key Insights

* **Average rating ≈ 4.2** → apps generally rate positively.
* **Top 10 apps** (e.g., Temple Run 2, Subway Surfers, Instagram) capture billions of installs.
* **Star Performers** dominate market share (\~64% of installs).
* **App size does not strongly determine installs**; small and large apps both succeed.
* **Content rating "Everyone"** accounts for the majority of installs.

## 🚀 Recommendations

* Focus on **improving “Popular but Poor” apps** with high installs but low ratings.
* Scale **“High Quality” apps** (high ratings but low installs) through marketing.
* Track **update frequency** as Avg\_Days\_Since\_Update strongly correlates with quality perception.
* Target categories like **Games, Tools, and Productivity** for maximum reach.


## 🛠 Tools & Technologies

* **Python (Pandas, Numpy, Matplotlib, Seaborn)** → Data Cleaning & Preprocessing
* **Power BI (DAX, Dashboarding)** → Modeling & Visualization

