# Movie_Data_Analysis_DBT

## Tool-by-Tool Deep Dive
1. Amazon S3 (Staging Layer)
•	Purpose: Used as the initial landing zone for raw CSV files (e.g., Netflix/MovieLens data).
•	Business Role: In enterprise settings like Netflix, S3 mimics a data lake — capturing structured or semi-structured logs of user activity, movie metadata, and ratings.
•	Scalability: S3 allows separation of storage from compute, useful for long-term archival and historical backtracking of raw events.
________________________________________
2. Snowflake (Data Warehouse)
•	Role: All raw files from S3 are ingested into Snowflake RAW schema tables.
•	Performance: It’s columnar, elastically scalable, and supports semi-structured formats (like JSON) — useful for storing customer preferences or watch history.
•	Business Value: For companies like Netflix, Snowflake centralizes siloed data into a single source of truth, supporting various consumer analytics (churn, engagement, recommendation performance).
________________________________________
3. DBT (Data Build Tool) – Core of the Project
🔹 Models (SQL Logic Layer)
•	Organized into:
o	Staging Models (stg_*): Standardize column names, formats, and apply light cleaning (removing nulls, type casting).
o	Intermediate Models (int_*): Join logic across domains like users + ratings or movies + tags.
o	Mart Models (dim_*, fct_*): Final analytics-ready tables for reporting (dim_movies, dim_users, fct_ratings).
🔹 Snapshots
•	Track slowly changing dimensions (SCD Type 2) — like tracking if a user changes their subscription tier or location.
•	Business Use: Essential for historical comparisons, such as comparing metrics “as known at the time.”
🔹 Tests
•	Built-in:
o	not_null, unique, accepted_values for column validations.
•	Custom:
o	e.g., “Every movie in ratings must exist in movies” → prevents data integrity issues.
•	Governance Benefit: Ensures data quality contracts are enforced between teams — crucial for regulated industries (finance, insurance).
🔹 Seeds & Sources
•	Seeds: Static reference tables (like genre mappings).
•	Sources: Declare raw tables from Snowflake (originating from S3).
🔹 Documentation
•	Auto-generated DAGs and lineage.
•	Teams can see upstream dependencies — great for debugging broken pipelines.
🔹 Macros (Jinja Templates)
•	Used for DRY code (Don't Repeat Yourself):
o	Example: Define a reusable current_timestamp() function.
•	Business Use: Promotes consistent transformation logic across 100s of tables (especially in multi-tenant SaaS systems).
________________________________________
4. Power BI / Looker Studio (Visualization Layer)
•	Final models (dim_, fct_) are exposed to Power BI for:
o	Heatmaps of top-rated genres
o	Average watch time across countries
o	Active user cohorts and rating patterns
•	For Netflix-like firms, this supports:
o	Personalized recommendation tuning
o	Subscriber engagement strategies
o	Content performance evaluation
________________________________________
📊 Business Use Cases – Deep Dive
🎬 For Media/Streaming (e.g., Netflix)
•	Churn Prediction: Track behavior changes via snapshots.
•	Recommendation Tuning: Use tag/rating analysis to optimize recommendation engines.
•	Content Performance: Aggregate metrics to find out which genres/actors drive higher engagement.
•	Geo Analytics: Understand what content performs best across regions — guide localization decisions.
🏦 Adaptation for Insurance (Your Scenario)
•	Policy Change Tracking: Use snapshots to track SCDs like plan upgrades or beneficiary changes.
•	Claims Analysis: Use DBT models to compute average claim settlement times, fraud detection heuristics.
•	Customer Segmentation: Power BI dashboards segment customers by policy type, age group, risk profile.
•	Regulatory Reporting: DBT tests ensure clean and auditable data for compliance (IRDAI, etc.)
________________________________________
🧠 Key Architectural Concepts Reinforced
•	ELT (not ETL): DBT embraces ELT (Extract → Load → Transform) using the warehouse’s power (Snowflake).
•	Modularity: Transformation logic split into small, reusable SQL models.
•	Data Contracts: Through testing + snapshots + documentation.
•	DevOps for Analytics: Git-based workflow, CI/CD for data models, similar to software engineering best practices.
