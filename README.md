# Analysis of Airbnb Investment Potential in Mexico City

## Repository Description
This repository aims to identify the top three boroughs (alcaldías) in Mexico City with the highest potential for investment in properties dedicated to vacation rentals via the Airbnb platform.

## Repository Structure
To achieve the above, this repository has the following structure:

```text
├── BASEF.csv              # This file contains all variables required for the OLS regression models.
├── Delitos.csv            # Data set of crimes reported in CDMX used for spatial union.
├── airbnb_limpio.csv      # Clean data set containing structural features (rooms, amenities) and location data from Airbnb listings in Mexico City.
├── Data_Challenge.ipynb   # Jupyter Notebook containing the sequential econometric workflow: cleaning, spatial join, EDA, and modeling.
└── README.md              # Summary of this project.
```
## Data Description
The database .csv files contain the following information:
### airbnb_limpio.csv
This dataset contains 26,401 observations and 20 variables. Each record corresponds to an active Airbnb listing in Mexico City. Key variables include:
* **Identification and Location:** `id`, `host_id`, `name`, `host_name`, `neighbourhood`, `latitude`, `longitude`.
* **Physical Characteristics:** `room_type`, `bathrooms`, `bedrooms`.
* **Pricing Structure and Rules:** `price`, `minimum_nights`, `maximum_nights`.
* **Review Dynamics:** `number_of_reviews`, `last_review`, `reviews_per_month`, `number_of_reviews_ltm`.
* **Host Supply and Availability:** `calculated_host_listings_count`, `availability_365`, `estimated_revenue_l365d`.

### Delitos.csv
This dataset aggregates georeferenced records of criminal incidents. Each observation includes the event date, crime category, and coordinates (latitude and longitude).

Using this base, criminality indicators were constructed for the surroundings of each Airbnb listing. A **k-Nearest Neighbors (k-NN)** algorithm was employed to identify all crimes occurring within a **500-meter radius** of each property. Four key variables were defined:
* `robos_500m`: Number of theft/robbery crimes registered within a 500m radius.
* `homicidios_500m`: Number of homicides and shooting events within 500m.
* `sexuales_500m`: Number of sexual crimes (rape, abuse, harassment) within 500m.
* `delitos_reales_500m`: Total count of real crimes within 500m, excluding records classified as "NO DELICTIVO".

### BASEF.csv
This is the final consolidated dataset resulting from the cleaning, feature engineering, and spatial join processes executed in the code. It serves as the direct input for the econometric models (`smf.ols`).

**1. Accommodation Data (Airbnb)**
Contains information for each property after outlier cleaning.
* **Identifiers and Location:** `id`, `neighbourhood` (Borough), `latitude`, `longitude`.
* **Structural Characteristics:**
    * `bedrooms`: Number of bedrooms.
    * `bathrooms`: Number of bathrooms.
    * `room_type`: Accommodation type (entire home, private room, etc.).
    * `n_amenities`: Generated variable counting the total amenities (WiFi, kitchen, etc.) offered.
* **Economic Variables:**
    * `price`: Original nightly price.
    * `price_ln`: Generated variable (Natural logarithm of price) to smooth distribution and allow elasticity interpretations.
    * `revenue_ln`: Logarithm of estimated revenue.
* **Activity:** `reviews_per_month`, `availability_365`.

**2. Security Data (Environmental Variables)**
These columns were generated via the spatial join (500-meter radius), indicating the crime count surrounding each Airbnb during the analyzed period:
* `robos_500m`: Count of reports classified as "ROBO".
* `homicidios_500m`: Count of reports of "HOMICIDIO" o "DISPAROS".
* `sexuales_500m`: Count of sexual crimes.
* `delitos_reales_500m`: Total count of criminal incidents excluding "HECHOS NO DELICTIVOS".
---

## Methodology (Data_Challenge.ipynb)

The Jupyter Notebook executes a sequential econometric workflow ranging from data cleaning to causal inference estimation:

### Preprocessing and Cleaning (ETL)
* **Variable Transformation:** Converted the `amenities` text column into processable lists and generated a new numeric variable `n_amenities`.
* **Outlier Removal:** Applied strict filters to remove atypical properties that could bias estimators.
* **Log-linearization:** Generated `price_ln` and `revenue_ln` variables to normalize price distribution and interpret coefficients as percentage semi-elasticities.

### Spatial Engineering (Spatial Join)
* **Spatial Tree Construction:** Utilized `BallTree` from `sklearn` with the Haversine metric to calculate real geodesic distances.
* **Radius Queries:** Searched for all crime reports within a 500-meter radius for each of the 26,000+ listings.
* **Crime Categorization:** Iterated over found incidents to create four new variables per listing based on text search.

### Exploratory Data Analysis (EDA)
* **Correlation Matrix:** A heatmap to detect multicollinearity between structural characteristics and criminality.
* **Analysis by Borough:** Bar charts comparing average crime incidence by zone.
* **Geospatial Mapping:** Scatter plots crossing latitude/longitude with prices capped at the 95th percentile to visualize spatial segregation of prices and crime.

### Econometric Estimation (OLS)
* Specified and estimated 5 linear regression models using Ordinary Least Squares (OLS) via `statsmodels`.
* **Error Correction:** All models use `cov_type='HC1'` (Heteroskedasticity-Robust Standard Errors), essential for real estate cross-sectional data.

---

## Econometric Models
The econometric models used are:
**Model 1: Structural Hedonic Pricing (Baseline)**
Includes fixed effects by borough (`C(neighbourhood)`) to capture the location "premium" independent of housing features.
* **Dependent Variable:** Log of price (`price_ln`).
* **Explanatory Variables:** Physical characteristics (`bathrooms`, `bedrooms`, `n_amenities`), operation rules (`minimum_nights`), and availability (`availability_365`).

**Model 2: Demand Estimation**
Seeks to understand demand elasticity with respect to price and how location influences listing popularity.
* **Dependent Variable:** Reviews per month (`reviews_per_month`), used as a proxy for occupancy rate or demand.
* **Explanatory Variables:** Price (`price_ln`), location (`neighbourhood`), amenities, and availability.

**Model 3: Interaction Effects (Location and Amenities)**
Evaluates if the marginal value of an additional amenity varies by city zone (e.g., if amenities are valued more in Polanco than in Iztapalapa).
* **Dependent Variable:** Log of price (`price_ln`).
* **Explanatory Variables:** Location, reviews, and interaction between borough and amenities (`C(neighbourhood)*n_amenities`).

**Model 4: Impact of Crime (No Spatial Controls)**
Measures the direct correlation between insecurity and rental price, without controlling for the intrinsic value of the zone.
* **Dependent Variable:** Log of price (`price_ln`).
* **Explanatory Variables:** Crime incidence in a 500m radius: Robbery (`robos_500m`), Homicide (`homicidios_500m`), and Sexual Crimes (`sexuales_500m`).

**Model 5: Complete Hedonic Model (Crime + Location)**
Isolates the effect of crime on price while holding location constant, allowing observation of whether insecurity *within* the same borough penalizes rental price.
* **Dependent Variable:** Log of price (`price_ln`).
* **Explanatory Variables:** All crime variables from Model 4 + Fixed effects by borough (`C(neighbourhood)`).

---

## AI Usage
ChatGPT was used as a technical assistant for:
* Python code construction.
* Review and formatting adjustments of working files (Tables and generated charts).
* Suggestions for database exploration and handling.
* Review and correction of writing style.

## Team Activities
Elideth and Juan Pablo carried out the econometric analysis, organized the results and prepared the final version of the document. Specifically, Juan Pablo examined the elements linked to the external basis, while Elideth defined the structure of the text and the presentation of the annexes. Both held independent work sessions to advance these activities and, subsequently, agreed on the final wording.

Alejandro defined the external base, carried out the merge process and carried out the comprehensive cleaning of both bases, with this he also ran the regressions that we defined in the team session. In addition, it generated the descriptive graphs necessary to understand the information, as well as those requested by Elideth and Juan Pablo. For his part, Guillermo prepared a preliminary and parallel cleaning of the original base (those shared for this exercise), as well as an annual value exercise by Airbnb and by neighborhood, as a first approximation.  Finally, he was in charge of developing and loading the GitHub repository.
