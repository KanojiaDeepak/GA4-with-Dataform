# GA4 E-commerce Analytics with Dataform

This Dataform project transforms raw Google Analytics 4 (GA4) e-commerce event data into actionable business reports. It's built with a layered approach for clean, maintainable data pipelines.

## Project Overview

We're analyzing purchase behavior from the public GA4 e-commerce dataset, focusing on which traffic sources drive the most value and volume. The workflow is designed for regular, automated runs.

---

## Data Layers

### Bronze Layer

* **Source:** `bigquery-public-data.ga4_obfuscated_sample_ecommerce.events_*`
* **Purpose:** Declares the raw, external GA4 events dataset. No transformations here; it's our direct link to the source.
* **File:** `definitions/bronze/ga4_events_declaration.sqlx`

### Silver Layer: `purchase_traffic_source_medium`

* **Purpose:** Cleans and denormalizes raw purchase data.
    * Filters for `purchase` events only.
    * Removes `(data deleted)` entries from `traffic_source.medium`.
    * Extracts `ga_session_id` and `items_quantity`.
* **Columns:** `event_date`, `traffic_source_medium`, `user_pseudo_id`, `ga_session_id`, `event_value_in_usd`, `items_quantity`.
* **File:** `definitions/silver/purchase_traffic_source_medium.sqlx`

### Gold Layer: `top_traffic_source_medium`

* **Purpose:** Business-ready report showing monthly performance by traffic source.
* **Aggregates:** By month and `traffic_source_medium`.
* **KPIs:**
    * `total_purchased_value_usd`
    * `total_purchases` (unique)
    * `total_items_purchased`
    * `avg_items_per_purchase`
* **File:** `definitions/gold/top_traffic_source_medium.sqlx`

---

## Persistence

All Silver and Gold layer outputs are created as **BigQuery tables**. This ensures data persists after each run, making it ready for queries and dashboards. The `type: "table"` configuration is ideal for our regular workflow, refreshing the data each time.

---

## Running the Workflow

* In the Dataform UI, simply **compile** then **start execution**. Dataform handles the dependencies and runs the layers in the correct order.
* You can schedule these runs directly within Dataform for automated daily or monthly updates.