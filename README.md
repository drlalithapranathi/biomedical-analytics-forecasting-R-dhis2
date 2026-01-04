# Maternal and Child Health Indicators in Sierra Leone

## Overview

This project analyzes maternal and child health indicators in Sierra Leone across the care continuum from pregnancy through early childhood. Using DHIS2 data from 13 districts and 149 chiefdoms, we apply **time series forecasting** (linear regression, SES, ARIMA), **geospatial analysis** (GIS mapping, hierarchical drill-down), and **deep learning** (ResNet50 transfer learning for computer vision) to identify service delivery gaps and data quality issues. Implementation leverages **R** (ggplot2, forecast, dplyr), **Python** (TensorFlow/Keras, pandas, numpy), and **DHIS2** for dashboard development and visualization.

**Analysis Framework:** Pregnancy (ANC, IPT) → Birth (skilled attendance) → Early Childhood (immunization, malnutrition surveillance)

## 1. Maternal Health Domain

#### **ANC IPT Coverage Trendline Analysis**
**Objective:** Review trendlines between ANC IPT 1 and ANC IPT 2 coverage to identify when they will meet.

**Methodology:**
- **Data:** Monthly ANC IPT 1 and IPT 2 coverage data (Jan 2024 - June 2025)
- **Analysis:** Linear regression using least squares method
- **Forecasting:** 6-month ahead projection using linear trendlines
- **Tools:** R (`lm()`, `ggplot2`)

**Key Findings:** IPT 1 consistently higher than IPT 2 with diverging trendlines (no intersection within forecast period), indicating worsening retention rates between doses

![Trendline Forecast](Visualizations-dashboard/Trendline%20forecast%206-month.png)

**Files:** `Trend forecasting.ipynb`

---

#### **SES Forecast for Western Area**
**Objective:** Forecast ANC IPT 2 coverage for December 2025 using Simple Exponential Smoothing.

**Methodology:**
- **Data:** Western Area district, Jan 2024 - Nov 2025
- **Model:** Simple Exponential Smoothing (SES) with two alpha values (α = 0.3, α = 0.7)
- **Evaluation:** Mean Absolute Error (MAE) for last 6 months
- **Tools:** R (`forecast` package, `HoltWinters()`)

**Key Findings:**
- **α = 0.3** (smoother forecasts): MAE = 6.44, December forecast = 94.47%
- **α = 0.7** (responsive to recent changes): MAE = 7.26, December forecast = 93.64%
- α = 0.3 model provided better short-term accuracy
- ANC IPT 2 coverage benefits from reducing month-to-month volatility

![SES Forecast](Visualizations-dashboard/SES%20ANC%20IPT%202%20coverage.png)

**Files:** `SES forecast for trendline.ipynb`

---

#### **Births by Skilled Attendants**
**Objective:** Identify districts with lowest births by skilled attendants and describe their geographic characteristics.

**Methodology:**
- **Data:** District-level births attended by skilled personnel (2024)
- **Derived Indicator:** Combined all birth types with skilled attendants
- **Analysis:** Geospatial mapping with OpenStreetMap layers
- **Tools:** DHIS2 Data Visualizer, GIS

**Key Findings:**
- **Bonthe** has the lowest births by skilled attendants
- Located in far southern coastal region with riverine/island terrain
- Other low-performing districts (Koinadugu, Kono, Pujehun) share similar challenges:
  - Remote from capital Freetown
  - Lack major highway infrastructure
  - Predominantly rural with low population density
  - Limited transport connectivity

**Geographic Barriers Impact:** Remoteness, difficult terrain, and weak infrastructure contribute to reduced access to skilled birth services.

![Births by Skilled Attendants](Visualizations-dashboard/births%20by%20skilled%20attendants%20q8.png)

## 2. Immunization Domain

#### **National Measles Coverage Analysis**
**Objective:** Analyze measles coverage for children <1 year and identify months missing the 60% target.

**Methodology:**
- **Data:** National monthly measles coverage (Jan 2024 - Dec 2025)
- **Indicator:** (Measles doses Fixed + Outreach) / Total population <1 year
- **Visualization:** Line chart with 60% threshold line
- **Tools:** R (`ggplot2`)

**Key Findings:**
- Coverage consistently **below 60% for ALL months**
- Range: 18% - 32%
- No month approaches the expected threshold
- Seasonal rises around August-October
- Reflects **persistent national underperformance** rather than isolated monthly declines
- Suggests systemic challenges in measles vaccination uptake and/or data completeness

![National Measles Coverage](Visualizations-dashboard/[IMAGE_NAME].png)

**Files:** `drilldown and coverage visualizations.ipynb`

---

#### **Drill-Down Analysis of Low Coverage**
**Objective:** Identify specific districts, chiefdoms, and facilities missing the 60% target.

**Methodology:**
- **Levels Analyzed:** District → Chiefdom → Facility
- **Derived Indicator:** Measles Doses <1y (replaced population denominator with 1)
- **Visualization:** Hierarchical drill-down maps and bar charts
- **Tools:** R (`dplyr`, `tidyr`), DHIS2 Maps

**Key Findings:**

**Districts:**
- Even highest-performing (Western Area) only reached 55.6%
- Most districts fall between 10-26%

**Chiefdoms:**
- 129 chiefdoms missed target
- Lowest-performing areas have highest concentration of "no-data" facilities
- Non-reporting facilities cluster in remote, hard-to-reach regions

**Facilities:**
- 85 facilities missed target
- Many show **high measles stock BUT zero doses recorded**
- Indicates data quality and reporting gaps rather than service absence
- CHC, CHP, and MCHP facility types analyzed

**Root Causes:** Both true service gaps AND significant under-reporting in rural areas.

![Drill-Down Analysis](Visualizations-dashboard/drilldown%20q5.png)

**Files:** `drilldown and coverage visualizations.ipynb`

---

#### **Trend Type Analysis**
**Objective:** Describe the type of trend in low measles vaccination coverage.

**Methodology:**
- **Temporal Analysis:** Monthly trends across 2024-2025
- **Geographic Analysis:** District clustering patterns
- **Data Quality Analysis:** Stock vs. doses administered discrepancies
- **Visualizations:** Line charts, bar charts, drill-down maps

**Key Findings:**

1. **Time-Based Trend:**
   - Chronically low throughout 2024-2025
   - All months far below 60% threshold
   - Modest fluctuations only
   - Indicates persistent systemic challenges, not temporary disruptions
   - Slight positive trend observed

2. **Geographic Trend:**
   - Low coverage clusters in remote and hard-to-reach districts
   - Facilities dispersed, road access limited
   - Chiefdoms show widespread service visibility gaps
   - Geographic barriers strongly shape immunization outcomes

3. **Reporting/Data Quality Trend:**
   - High measles stock issuance BUT zero/missing doses administered
   - Substantial under-reporting and data completeness issues
   - Artificially depresses coverage values
   - Obscures true performance

**Conclusion:** Line graph (when), district bar chart (where), and drill-down map (why) provide complementary perspectives on coverage gaps.

**Files:** `drilldown and coverage visualizations.ipynb`

---

#### **Dropout Rate Correlation**
**Objective:** Analyze correlation between Penta1-Measles dropout rate and measles coverage.

**Methodology:**
- **Data:** All districts, Jan 2024 - June 2025
- **Indicators:** Dropout rate (Penta1 → Measles) and Measles Coverage <1y
- **Analysis:** Pearson correlation coefficient
- **Visualization:** Grouped bar chart by district
- **Tools:** R (`cor()`, `ggplot2`)

**Key Findings:**
- **Pearson correlation: -0.295** (weak negative correlation)
- Districts with higher dropout rates tend to have slightly lower measles coverage
- Relationship is NOT strong
- Substantial variation across districts exists
- No strong or consistent inverse relationship

**Interpretation:** While reducing dropout may contribute to small improvements in measles coverage, broader system-level and operational issues must also be addressed to achieve substantial gains in immunization performance.

![Dropout vs Coverage Comparison](Visualizations-dashboard/q7.png)

![Dropout Correlation](Visualizations-dashboard/corr%20q7.png)

**Files:** `drilldown and coverage visualizations.ipynb`

---

## 3. Child Malnutrition Domain

#### **Malnutrition Rates by Chiefdom**
**Objective:** Identify chiefdom with lowest malnutrition rate and high-malnutrition chiefdoms near Freetown.

**Methodology:**
- **Data:** Chiefdom-level weight-for-height <79% rate (2024-2025)
- **Derived Indicator:** Combined severe (<70%) and moderate (70-79%) malnutrition
- **Visualization:** Geospatial choropleth map with Google Streets layer
- **Tools:** DHIS2 Maps, Sentinel-2 satellite imagery

**Key Findings:**
- **Lowest:** Malema (2.4%)
- **Highest near Freetown:**
  - Kaffu Bullom (33.21%)
  - Lokomasama (34.77%)
- Peri-urban areas near capital show unexpectedly high malnutrition
- May reflect rapid urbanization, displacement, or service delivery gaps

![Malnutrition Rates by Chiefdom](Visualizations-dashboard/3a%20malnutrition%20rates.png)

**Files:** Presentation slides (maps generated via DHIS2)

---

#### **Predicting Severe Malnutrition Using Computer Vision**
**Objective:** Predict severe malnutrition rate for Bo district (November 2025) using monthly choropleth maps and a deep learning model.

**Methodology:**

**Data Preparation:**
1. 21 monthly choropleth maps (Jan 2024 - Sep 2025) downloaded from DHIS2
2. District segmentation using region-growing algorithm
3. Six malnutrition bins created based on data distribution: 0-<4, 4-<5, 5-<6, 6-<7, 7-<9, 9+
4. Maps recolored with fixed yellow→orange→red palette
5. Maps standardized and saved as PNG files

![District Segmentation](Visualizations-dashboard/district%20segmentation.png)

**Model Architecture:**
- **Base Model:** ResNet50 (pretrained on ImageNet)
- **Transfer Learning:** Frozen convolutional layers used as feature extractor
- **Custom Head:** GlobalAveragePooling2D → Dense(128, ReLU) → Dropout(0.5) → Dense(6, Softmax)
- **Input:** 224×224×3 RGB images
- **Output:** Probability distribution over 6 malnutrition bins

**Training:**
- **Train/Test Split:** 70/30 (14 train, 6 test, 1 holdout)
- **Epochs:** 30
- **Batch Size:** 2
- **Optimizer:** Adam (learning rate = 1e-4)
- **Loss:** Categorical cross-entropy

**Two Models Compared:**
1. **Unmasked Model:** Full map images (Bo visible)
2. **Masked Model:** Bo district region replaced with neutral gray

**Results:**

| Model | Accuracy | F1 (Macro) | Predicted Bin (Sep 2025) | True Bin |
|-------|----------|------------|--------------------------|----------|
| Unmasked | 0.667 | 0.800 | 5-<6 | 4-<5 |
| Masked | 0.667 | 0.800 | 5-<6 | 4-<5 |

**Key Insights:**
- Both models achieved identical 67% accuracy, predicting bin 5-<6 (one bin higher than true 4-<5 with probability 44.5%)
- **Spatial patterns from other districts contain predictive signal** even when Bo is masked
- Limited dataset size (21 images) constrains model accuracy

![Masked Model Comparison](Visualizations-dashboard/masked%20model.png)

**Files:** `deeplearning-resnet50-imaging.ipynb`

---

### 4. Cross-Cutting Analysis

#### **Dashboard Creation**
A comprehensive DHIS2 dashboard was created consolidating all key visualizations:
- ANC IPT Coverage Trends
- Births by Skilled Attendants (Map)
- Total Malnutrition Rates (Map)
- National Measles Coverage (Line chart)
- District Measles Coverage (Bar chart)
- Drill-down Chiefdom and Facility Measles Coverage (Map)
- Dropout vs. Measles Coverage Comparison (Bar chart)

**Purpose:** Provides stakeholders with integrated view of maternal and child health service delivery across Sierra Leone.

![DHIS2 Dashboard](Visualizations-dashboard/dashboard.png)

---

#### **Data Validation Rules**
**Objective:** Identify validation rules and data quality issues.

**Three Critical Validation Rules:**

1. **Consistency Rule:**
   `Live_Births + Still_Births = Total_Births`
   Ensures internal consistency in birth reporting.

2. **Inventory Rule:**
   `Measles_Stock_Dispensed ≤ Measles_Stock_On_Hand`
   A facility cannot dispense more vaccine than available stock.

3. **Demographic Rule:**
   `Population_<1yr < Population_<5yr`
   Demographically, children <1 must be subset of children <5.

**Data Quality Issues Identified:**

1. **Widespread missing values** for doses administered (<1 year)
   - Many facilities reported stock but no doses
   - Artificially low or zero coverage

2. **Inconsistent/outdated population denominators**
   - Extremely small or unusual "population <1" values
   - Produces coverage >100% or high volatility

3. **Facilities not reporting** despite receiving vaccines
   - Non-reporting sites cause geographic blind spots
   - Concentrated in remote locations

4. **Over-reporting or duplicate entries** in urban facilities
   - Implausibly high doses relative to catchment size
   - Suggests data entry duplication

5. **Spatial reporting biases**
   - Urban facilities: complete data
   - Rural/riverine/hard-to-reach chiefdoms: data gaps
   - Systematic geographic bias in reporting

---

## Critical Insights Identified

1. **Diverging ANC Retention Patterns:** IPT 1 and IPT 2 trendlines moving apart rather than converging, indicating worsening dropout rates between first and second visits

2. **Systematic Measles Underperformance:** National coverage chronically below 60% target across ALL months (18-32%), suggesting structural barriers beyond seasonal variation

3. **Geographic Clustering of Service Gaps:** Remote districts (Bonthe, Koinadugu, Kono, Pujehun) consistently show lowest performance across multiple indicators, driven by infrastructure and accessibility challenges

4. **Urban Malnutrition Paradox:** Peri-urban areas near Freetown (Kaffu Bullom 33%, Lokomasama 35%) show unexpectedly high malnutrition rates, contradicting typical urban health advantages

5. **Data Quality vs. Service Delivery Duality:** High vaccine stock issuance with zero recorded doses administered indicates both true service gaps AND significant reporting/data completeness issues

6. **Weak Dropout-Coverage Correlation:** Pearson correlation of -0.295 between Penta1-Measles dropout and coverage reveals that retention alone cannot explain low immunization performance

7. **Spatial Autocorrelation in Malnutrition:** Deep learning model successfully predicted district-level malnutrition using spatial patterns from neighboring regions (67% accuracy), demonstrating geographic interdependence

8. **Model Performance Insights:** SES forecasting showed smoother models (α=0.3, MAE=6.44) outperformed responsive models (α=0.7, MAE=7.26), suggesting value in reducing noise over tracking volatility

---

## Limitations

1. **Small Dataset for Deep Learning:**
   - Only 21 monthly images for malnutrition prediction
   - Limits model generalization and accuracy

2. **Missing Data:**
   - 11 missing values in malnutrition dataset
   - Incomplete facility reporting affects coverage calculations

3. **Linear Model Assumptions:**
   - Trendline forecasts assume no policy changes or interventions

4. **Temporal Coverage:**
   - Analysis limited to 2024-2025


---

## Authors

- Lalitha Pranathi Pulavarthy
- Erica Babb
- Haritha Reddy Karingula
- Ross Prater
- Ying Liu

**Instructor:** Dr. Saptarshi Purkayastha