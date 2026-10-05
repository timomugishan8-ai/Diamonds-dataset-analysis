# Data Dictionary

| Variable | Type | Meaning | Typical range in source data |
|---|---|---|---|
| `price` | Numeric | Diamond price in US dollars | $326–$18,823 |
| `carat` | Numeric | Diamond weight in carats | 0.20–5.01 |
| `cut` | Categorical | Cut quality | Fair, Good, Very Good, Premium, Ideal |
| `color` | Categorical | Colour grade; D is best and J is worst | D–J |
| `clarity` | Categorical | Clarity grade; I1 is lowest and IF is highest | I1–IF |
| `depth` | Numeric | Total depth percentage | 43–79 |
| `table` | Numeric | Width of the top relative to the widest point | 43–95 |
| `x` | Numeric | Length in millimetres | 0–10.74 |
| `y` | Numeric | Width in millimetres | 0–58.9 |
| `z` | Numeric | Depth in millimetres | 0–31.8 |

## Analytical interpretation

- **Carat** represents weight and is the strongest single numeric correlate of price in this analysis.
- **Cut, colour and clarity** are categorical quality-related attributes.
- **x, y and z** describe physical dimensions and are strongly correlated with one another and with carat.
- **Depth and table** show comparatively weak linear relationships with price.

The source dataset contains 53,940 observations and 10 variables. citeturn1search1turn1search5
