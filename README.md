# Weather_Crime_Project
# Introduction
## *Meteorological Crimes Project: Data Strategy & Engineering*
Introduction
This project focuses on the data lifecycle as an enabler of strategic decisions. The main objective was to build a pipeline for consolidating heterogeneous data to validate the correlation between meteorological variables and crime patterns. More than a modeling exercise, this project is a demonstration of how to transform raw, unstructured data into an information asset with high predictive value.

Phase 1: Data Engineering and Refinement (The Core of the Project)
The biggest challenge was not choosing the model, but orchestrating a data infrastructure capable of supporting complex analyses.

Data Architecture:
Ingestion and Consolidation: Manual integration of 5 independent data sources (crime history and meteorological records).

Cleaning and Normalization: Development of scripts for handling null values, time inconsistencies, and disparate meteorological unit formats.

Strategic Feature Engineering:

Creation of the Severity Index: Instead of processing raw labels, I designed a synthetic "Violence" metric to reduce noise and improve the data signal.

Treatment of Imbalances: Implementation of resampling techniques (SMOTE) to mitigate bias in the class distribution, a critical step to ensure the integrity of any subsequent analysis.

Technical Results of the Structure:
Data Quality: A production-ready data structure was achieved, increasing the system's responsiveness from 34% to 79% accuracy by improving sample representativeness.

Phase 2: Architecture Optimization and Data Governance (Ongoing)
The second iteration moves away from the "MVP" to focus on scalability and observability.

Engineering Roadmap:
Dataset Enrichment (2015-2025): Expanding the time horizon to capture long-term climate cycles and changes in post-pandemic crime trends.

Categorical Variable Pipeline: Implementing advanced coding processes for new socio-meteorological variables.

My role in this project demonstrates that AI is secondary to data strategy. A model is only as good as the engineering behind it. I have led this process from raw data capture to the delivery of a refined, validated dataset ready to generate real impact on public safety decision-making.
