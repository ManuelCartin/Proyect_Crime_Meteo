# Weather_Crime_Project
# Introduction
## *"Meteorological Crime" Project: A Hybrid Predictive Model* 
This project was born from my interest in leading the full lifecycle of an Artificial Intelligence product. My goal was to validate the hypothesis that there is a relationship between criminal activity and meteorological variables, in order to develop a predictive model that could deliver real value. This project allowed me to integrate my passion for product management with a solid technical foundation, demonstrating that effective leadership is built on an understanding of data engineering challenges and strategic design.

## Version 1.0: The Minimum Viable Product (MVP)
The first phase was a proof of concept focused on validating the initial hypothesis. The main challenge was not the model itself, but rather the data engineering, which consumed most of the effort. I had to consolidate and clean five different datasets of historical crimes and meteorological variables without the help of a tutorial.

### Key Strategic Decisions:

Data Bias Management: The initial hypothesis yielded an accuracy of 34%. After identifying extreme class imbalance, I made the strategic decision to apply oversampling techniques (SMOTE). This not only improved performance but also validated the project's viability, raising accuracy to 79%.

Classification Redesign: Instead of treating each crime as an individual class, I created a violence index to group crimes. This key decision simplified the model and improved pattern detection, which was the true differentiating factor.

The code for this version and the data used can be found in crime_weather_final (1), meteo_sj_crime_merge_clean.xlsx, and other repository files.

## Version 2.0: Optimization and Explainability (In Progress)
The second iteration of the project builds on the learnings from V1.0. Currently, I'm focusing on model optimization and explainability, crucial aspects for an AI product to gain adoption and build trust.

### Roadmap:

Expanding the Time Range (2015-2025): To enrich the model with more diverse and representative data.

Incorporating New Variables: New categorical variables related to crime and climate are being added.

Improving the Violence Index: Optimizing classification for greater accuracy.

Focusing on Explainability: Using techniques like SHAP or LIME to better understand why the model makes certain decisions, which is critical for deployment in a real-world environment.

## Lessons from a PM:

This project taught me that data engineering is 80% of the work and that product design (such as creating the violence index) is what really makes the difference. An AI product leader must understand and prioritize these challenges to guide the team toward a successful solution with real value.
