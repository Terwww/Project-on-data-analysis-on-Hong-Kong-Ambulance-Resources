# Project-on-data-analysis-on-Hong-Kong-Ambulance-Resources

# Kowloon West Ambulance Performance & Resource Optimisation Analysis (2025)

## Project Overview

This project analyses the full-year 2025 Kowloon West ambulance call dataset (approximately 111,000 incidents across 42 columns) to identify performance gaps against the 12-minute response time pledge, understand factors driving longer response and conveyance times, and propose data-driven resource reallocation recommendations under fixed capacity constraints.

The analysis is designed as a practical portfolio piece for a career transition into data analytics. It combines domain knowledge from frontline ambulance operations with statistical testing, feature engineering, predictive modelling, and operational decision-support techniques.

## Business Problem

Hong Kong’s ambulance service operates under a performance pledge that a high percentage of emergency calls should receive an ambulance on scene within 12 minutes. Overall Kowloon West performance in 2025 stood at approximately 94.8%, slightly below the territory-wide benchmark of 95.9%. Certain areas (notably TTG) recorded substantially lower on-time rates (around 81.7% in some analyses), with clear deterioration during specific hours, particularly midday and evening peak periods.

Key questions addressed:

- Which areas and time periods show the poorest adherence to the 12-minute pledge?
- What factors (location, hour of day, incident type, cyclic time patterns) most strongly influence response time?
- Given that additional ambulances cannot simply be added, which stations/areas have relatively higher slack and can temporarily support higher-need locations during peak windows, while limiting performance degradation in the donor area?
- How does conveyance time from scene departure to hospital arrival vary by hospital and time of day for the main emergency incident types, and does rush hour systematically lengthen hospital travel time?

The ultimate goal is to produce actionable, geographically realistic recommendations that improve overall system performance without requiring net additional resources.

## Data Ethics, Privacy & Governance

This project was conducted with explicit organisational approval from the data-owning entity. All personal and sensitive information was removed or irreversibly de-identified prior to analysis.

In particular:

- No names, exact addresses, patient identifiers, phone numbers, or other direct personal identifiers are present in the analytical dataset.
- Location information was aggregated to area/location-code level.
- Timestamp data was retained only at the level necessary for operational analysis and was handled in accordance with data-minimisation principles.
- The analysis was limited to the approved purpose of performance evaluation and resource-planning insight; no secondary use of the data was undertaken.

These practices align with the privacy principles set out in Canada’s *Personal Information Protection and Electronic Documents Act* (PIPEDA) and with commonly accepted Canadian public- and private-sector data-governance expectations, including:

- Accountability  
- Identifying purposes  
- Consent (organisational approval obtained)  
- Limiting collection and use  
- Safeguards and de-identification  
- Openness regarding the purpose of the analysis  

The resulting dataset used for modelling and visualisation contains only aggregated operational metrics suitable for non-identifying performance analysis. No attempt was made to re-identify individuals, and no personal health information is retained or disclosed.

## Dataset

- Scope: Full calendar year 2025, Kowloon West region.
- Volume: Approximately 111,000 individual incidents.
- Key fields used: call time, arrival at scene, departure to hospital (e99_time), arrival at hospital (arh_time), location/area code, incident type, hospital destination, and related operational timestamps.
- Challenges addressed: impossible timestamps (call time after scene arrival), duplicates, cancelled/false calls, prolonged or special cases, missing values for non-conveyance incidents, and extreme outliers.

## Methodology and Analytical Steps

### 1. Data Cleaning and Feature Engineering
- Converted timestamp columns to proper datetime format and calculated response time (scene arrival − call time) and conveyance time (hospital arrival − scene departure) in minutes.
- Filtered invalid records (negative or impossible times), cancelled cases, non-emergency or non-relevant incident types where appropriate, and extreme outliers (e.g., response times beyond reasonable operational bounds; conveyance times > 60–120 minutes depending on the specific analysis).
- Engineered features including hour of day, day-of-week indicators, binary rush-hour flags (typically 07:00–09:00 and 17:00–19:00), cyclic time encodings (sine and cosine of minutes past midnight) to respect the circular nature of time, and simplified area groupings.

### 2. Exploratory Performance Analysis
- Calculated overall and segmented 12-minute on-time rates by area and hour.
- Produced heatmaps of on-time performance by hour × area, clearly highlighting under-performing combinations (particularly TTG during midday and evening peaks).
- Compared distributions of response times (boxplots and summary statistics) between TTG and other areas.
- Examined incident-type breakdowns to identify which call categories contributed most to performance shortfalls.

### 3. Statistical Validation
- Independent t-test comparing mean response times between TTG and other areas.
- Chi-square test of association between area group and on-time status.
- Logistic regression modelling the probability of meeting the 12-minute pledge, controlling for hour and other factors, to quantify the independent effect of location (odds ratios and significance).

These tests confirmed that observed differences were statistically significant and not merely random variation.

### 4. Predictive Modelling of Response Time
- Baseline linear regression using cyclic (sin/cos) time features, area indicators, and hour.
- Improved models incorporating incident type and interaction terms.
- Random Forest regression to capture non-linear relationships and interactions; feature-importance rankings showed cyclic time features and certain area–time interactions as dominant predictors.
- Model iteration demonstrated progressive improvement in explanatory power while remaining transparent enough for operational discussion.

### 5. Resource Reallocation Analysis (Fixed Capacity)
Because net capacity cannot be increased, the analysis shifted from “add more units” to “reallocate existing units more intelligently.”

- Constructed area-level demand (need) scores based primarily on on-time rate (lower rate = higher need).
- Identified relatively stronger-performing areas as potential donors (higher on-time rate indicating greater relative slack).
- Built a simple distance/closeness matrix between key Kowloon West areas to ensure only geographically feasible moves were considered.
- Developed a composite Priority Score that balances recipient need, donor performance, and geographic proximity.
- Ranked feasible donor–recipient pairs by hour slot, prioritising short-distance moves during the highest-need windows (especially midday and evening peaks involving TST/TTG and nearby stronger areas such as YMT).

This approach produces realistic temporary reallocation suggestions rather than idealised unconstrained solutions.

### 6. Hospital Conveyance Time Analysis
Focused on the four major receiving hospitals (CMC, KWH, QEH, PMH) and the primary emergency incident types (EMA, EMAC, EMAT).

- Calculated conveyance time from scene departure to hospital arrival.
- Filtered null/non-conveyance records and extreme cases (> 1 hour).
- Built separate ordinary least squares (OLS) regression models for each hospital, including rush-hour indicators, hour, and cyclic time features.
- Produced heatmaps of average conveyance time by 2-hour time slots × hospital to visualise temporal patterns.
- Key finding: rush-hour effects and cyclic time patterns vary by hospital; one hospital in particular showed a clearer, statistically significant lengthening of conveyance time during peak periods.

Hospital-specific models support more targeted operational discussions with individual hospital partners.

## Key Findings (Summary)

- Certain areas (notably TTG and related zones) and specific hour windows (midday and evening peaks) consistently under-perform the 12-minute pledge.
- Time-of-day (especially cyclic encoding) and area–time interactions are strong drivers of response time variation.
- Limited, geographically close reallocation during peak windows can improve performance in the weakest cells while keeping donor-area degradation modest.
- Conveyance times to the four main hospitals show hospital-specific sensitivity to rush hour; understanding these differences enables better coordination between ambulance control and receiving hospitals.

## Business Solutions & Recommendations

1. **Targeted temporary reallocation during peak windows**  
   Prioritise short-distance moves from relatively stronger neighbouring areas into the highest-need cells (especially TST/TTG corridors) during the hours identified by the Priority Score analysis. Restrict moves to operationally feasible distances and treat them as temporary peak-period adjustments rather than permanent redeployments.

2. **Hospital-specific coordination**  
   Share the hospital-level conveyance models and heatmaps with the relevant hospital management teams. Discuss route optimisation, traffic coordination, or priority access arrangements particularly for hospitals showing stronger rush-hour effects.

3. **Focus on high-impact incident types and time slots**  
   Concentrate additional attention and any available surge capacity on the incident types and hour windows that drive the largest performance gaps.

4. **Monitoring and feedback loop**  
   Implement ongoing tracking of on-time rates by area × hour slot and conveyance times by hospital. Revisit the Priority Score rankings periodically as call patterns evolve.

5. **Future enhancements**  
   Incorporate real-time traffic or call-volume predictors, explore more advanced optimisation (e.g., linear programming under explicit capacity constraints), and extend the analysis to multi-day patterns or weather effects if additional data become available.

## Tools & Skills Demonstrated

- Data cleaning and feature engineering (Pandas, datetime handling, outlier treatment)
- Exploratory data analysis and visualisation (heatmaps, boxplots, time-series style plots)
- Statistical inference (t-tests, chi-square, logistic regression)
- Predictive modelling (linear regression, Random Forest, cyclic feature engineering)
- Operational decision support (distance-aware prioritisation, composite scoring for resource moves)
- Clear communication of findings and recommendations suitable for both technical and operational stakeholders
- Awareness of privacy principles and responsible data handling consistent with Canadian privacy legislation (PIPEDA)

## How to Use This Repository

The notebooks contain sequential steps from raw data loading and cleaning through EDA, statistical testing, modelling, reallocation analysis, and hospital conveyance modelling. Each major section includes commentary explaining the business rationale and technical choices.

This project demonstrates the ability to translate a real operational performance challenge into a structured analytics workflow, validate insights statistically, convert those insights into practical constrained recommendations, and handle data responsibly in accordance with privacy and ethical standards—skills directly relevant to a data analyst role in operations, healthcare, or emergency services.

---

*This analysis was performed solely for learning, demonstration, and professional portfolio purposes. All personal and sensitive information was removed prior to analysis, and organisational approval was obtained for the use of the de-identified operational data.*