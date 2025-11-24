# Data Challenge: Airbnb Market in CDMX

## Project Description
This repository contains the solution to the **Data Challenge**. The project aims to evaluate the economic viability and safety of Airbnb investments in Mexico City (CDMX) by integrating real estate listings with official crime incidence data.

The core of the analysis involves a **Spatial Join** between accommodation coordinates and investigation files from the Attorney General's Office (FGJ) to quantify the impact of local security on pricing and occupancy rates.

## Challenge Objectives
1.  **Data Engineering:** Cleaning, transformation, and merging of disparate datasets (Airbnb + Crime Reports).
2.  **Spatial Analysis:** Efficient mapping of specific crimes (Burglary, Homicides, Sexual Offenses) within a **500-meter radius** of each property using KNN algorithms.
3.  **Econometric Modeling:** Estimation of a OLS regression to isolate the marginal value of structural and locational attributes.
4.  **Visualization:** Geospatial plotting and correlation matrices to support investment decision-making.
5.  **Structure of this repo**
```text
├── BASEF.csv               # This file contains all variables required for the OLS regression models.
├── Delitos.csv             # Data set of crimes reported in CDMX used for spatial union.
├── airbnb_limpio.csv       # Clean data set containing structural features (rooms, amenities) and location data from Airbnb listings in Mexico City.
├── Data_Challenge.ipynb    # 
└── README.md               # Summary of this proyect
```
## Prerequisites
To run this project, you need **Python 3.8+** and the following libraries.

### Key Libraries
* `pandas` & `numpy` (Data Manipulation & Calc)
* `matplotlib` & `seaborn` (Data Visualization)
* `statsmodels` & `scikit-learn` (Statistical Modeling & Spatial Algorithms)

**Quick Install:**
```bash
pip install pandas numpy matplotlib seaborn statsmodels scikit-learn
