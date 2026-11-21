# Assignment 3: Two Averages, One Argument

**Muhammad Wasay | ERP: 36098**

This notebook analyzes the cleaned Week 2 teaching dataset. These observations are illustrative teaching figures rather than actual PBS records. Below, we compute the mean, median, and endpoint percentage change across each item and city, followed by an outlier sensitivity check on Sweetener D.

```python
import pandas as pd
import numpy as np

prices_df = pd.read_csv("week2_cleaned_prices.csv")
prices_df["date"] = pd.to_datetime(prices_df["date"])
prices_df["price"] = pd.to_numeric(prices_df["price"], errors="coerce")

print("Total rows:", len(prices_df))
print("Missing price count:", prices_df["price"].isna().sum())
print("Unique items:", sorted(prices_df["item"].dropna().unique()))
print("Unique cities:", sorted(prices_df["city"].dropna().unique()))
```

## Item–city summary: mean, median, and endpoint percentage change

```python
summary_records = []
for (item_name, city_name), subset in prices_df.groupby(["item", "city"]):
    subset = subset.sort_values("date")
    non_null = subset.dropna(subset=["price"])
    start_row = non_null.iloc[0]
    end_row = non_null.iloc[-1]
    pct_diff = (end_row["price"] - start_row["price"]) / start_row["price"] * 100
    summary_records.append({
        "item": item_name,
        "city": city_name,
        "valid_observations": len(non_null),
        "mean": non_null["price"].mean(),
        "median": non_null["price"].median(),
        "first_valid_date": start_row["date"].date(),
        "first_valid_price": start_row["price"],
        "last_valid_date": end_row["date"].date(),
        "last_valid_price": end_row["price"],
        "first_to_last_change_pct": pct_diff
    })

city_item_summary = pd.DataFrame(summary_records).sort_values(["item", "city"])
city_item_summary.round(2)
```

Each endpoint percentage change relies strictly on the first and last **valid** entries within that specific item–city pair. Because Pulse C in City 2 has no recorded price on 1 January, its calculation begins on 8 January and runs through 19 March.

## Combined item-level averages across both cities

```python
item_pooled = prices_df.groupby("item")["price"].agg(
    valid_observations="count",
    mean="mean",
    median="median"
).reset_index()
item_pooled.round(2)
```

## Outlier sensitivity check: Sweetener D in City 1

```python
sweetener_c1 = prices_df[(prices_df["item"] == "Sweetener D") & (prices_df["city"] == "City 1")].copy()
clean_series = sweetener_c1["price"].dropna()

# Week 2 audit notes show the raw entry was 9,999 before being corrected to 157.5 on 22 January 2026.
outlier_idx = sweetener_c1.index[
    sweetener_c1["price_original"].astype(str).str.replace(",", "", regex=False).eq("9999")
][0]

uncleaned_series = clean_series.copy()
uncleaned_series.loc[outlier_idx] = 9999.0

sensitivity_table = pd.DataFrame([
    {"version": "Week 2 corrected value", "mean": clean_series.mean(), "median": clean_series.median()},
    {"version": "Original 9,999 value used", "mean": uncleaned_series.mean(), "median": uncleaned_series.median()}
])
sensitivity_table.round(2)
```

Reintroducing the raw `9,999` figure causes the mean to spike dramatically while leaving the median nearly unchanged. This demonstrates why the arithmetic mean cannot automatically be treated as a "typical" value whenever unverified extreme outliers may exist in the data.