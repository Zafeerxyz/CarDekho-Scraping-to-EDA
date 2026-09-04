# CarDekho New Cars — Web Scraping, Feature Engineering & EDA

An end-to-end data analysis project on India's new car market, built entirely from
live-scraped data — covering dynamic web scraping, data cleaning, feature engineering,
and exploratory data analysis to understand what drives car pricing and specifications.

## Objective

To collect real-world new car listing data directly from CarDekho and analyze the
relationships between price, engine specifications, mileage, ratings, and vehicle type
(including electric vehicles) across the Indian new car market.

## Data Collection

Raw data was collected using **Selenium**, since CarDekho's listing page loads results
incrementally via a "Load More" button rather than standard pagination. The scraper:

- Detects the total listed car count directly from the page (e.g. "293 Cars in India
  With Search Options") and uses it as a dynamic target, instead of a hardcoded limit
- Clicks "Load More" repeatedly until the target is reached, with a streak-based
  fallback to stop early if the page stops returning new listings
- Extracts five raw fields per car: name, rating, price range, mileage, and engine
  displacement
- Saves the result to `cardhekho_new_cars.csv`

## Project Workflow

| Notebook | Description |
|---|---|
| `Untitled.ipynb` | Web scraping — Selenium-based collection of new car listings from CarDekho, handling dynamic pagination |
| `cleaning_feature_eng_analysis.ipynb` | Data cleaning, feature engineering, and exploratory data analysis |

### Feature Engineering
- Split the raw `price` text field (e.g. `"Rs.11.99 - 19.19 Lakh*Get On-Road Price..."`)
  into clean numeric `price_min_lakh` and `price_max_lakh` columns, normalizing mixed
  **Lakh** and **Crore** units onto a single comparable scale
- Extracted `company` (brand) from the car name
- Derived `price_segment` — five categorical buckets from Budget to Luxury & High-End
- Derived `price_avg_lakh` for a single continuous price feature
- Flagged `is_electric` using keyword detection on car name

### Data Cleaning
- Renamed and standardized raw fields (`mileage` → `mileage_km_per_liter`,
  `engine` → `engine_cc`), stripping units and converting to numeric types
- Handled missing values contextually rather than with a single global fill:
  - Rating gaps filled with the overall median
  - Electric vehicles correctly set to 0 for `engine_cc` and `mileage_km_per_liter`
    (they have neither), instead of being imputed with fuel-vehicle values
  - Remaining gaps filled using the **median within each price segment**, since engine
    size and mileage vary substantially across price tiers

### Exploratory Data Analysis
- **Univariate analysis** — price segment distribution, brand frequency, engine size
  distribution
- **Outlier detection** — IQR method applied to engine displacement, isolating a small
  set of genuine ultra-luxury outliers (Bentley, Rolls-Royce, Ferrari) rather than data
  errors
- **Bivariate analysis** — engine size vs. mileage, rating across price segments,
  electric vehicle distribution across price segments
- **Correlation analysis** — heatmap across all numeric features to identify the
  strongest relationships in the dataset

## Key Files

- `cardhekho_new_cars.csv` — raw scraped output
- Cleaned & feature-engineered dataset (post-processing, used for EDA)

## Key Insights

- **Engine size is a strong predictor of top-end pricing but a weak one for entry-level
  pricing** — brand positioning matters more than engine size for base variants
- **Electric vehicles behave differently from the rest of the market**, showing strong
  negative correlation with both engine size and mileage (expected, since they have
  neither), and are concentrated in the mid-to-premium price segments
- **Customer rating is largely independent of price** — budget and luxury cars are
  rated on comparable terms, suggesting ratings reflect satisfaction relative to a
  car's own class rather than absolute price or performance
- **The brand distribution is heavily long-tailed** — a handful of mainstream brands
  dominate listing volume, while 15+ ultra-luxury brands are represented by only 1-3
  models each

## Tools & Libraries

- Python
- Selenium (web scraping)
- Pandas, NumPy
- Matplotlib, Seaborn
- SciPy (statistical testing)
