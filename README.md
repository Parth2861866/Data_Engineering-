# E-Commerce Data Warehouse Pipeline (ELT)

A robust Data Engineering project demonstrating the replication of production-grade relational data into a cloud-native data warehouse. This pipeline utilizes **Airbyte** for ingestion and **Google BigQuery** for centralized storage and transformation.

---

## 🏗️ Architecture
The pipeline follows a modern **ELT** (Extract, Load, Transform) pattern:
1. **Source**: PostgreSQL (Transactional data including Customers, Orders, and Products).
2. **Ingestion**: Airbyte (Self-hosted/Cloud) managed via Service Account authentication.
3. **Destination**: Google BigQuery (Enterprise Data Warehouse).
4. **Environment**: Google Cloud Platform (GCP).

## 🛠️ Tech Stack
* **Data Integration**: [Airbyte](https://airbyte.com/)
* **Data Warehouse**: [Google BigQuery](https://cloud.google.com/bigquery)
* **Infrastructure**: GCP IAM (Service Accounts & Roles)
* **Language**: SQL (Data Transformations)

## 📂 Data Model
The following entities are synced from the `public` schema of the source database:
* `customers`: User profiles and contact information.
* `orders`: Transaction headers and timestamps.
* `order_items`: Line-item details connecting products to orders.
* `products`: Catalog details and pricing.

## 🚀 Implementation Steps

### 1. Cloud Infrastructure & Security
* Created a dedicated **GCP Service Account** (`airbyte`) to facilitate secure, programmatic access to BigQuery.
* Assigned the **BigQuery Data Editor** role to ensure the pipeline can write and update schemas without full administrative access (Principle of Least Privilege).
* Initialized two primary datasets:
    * `raw_dataset`: The landing zone for unmodified source data.
    * `transformed_data`: The analytical layer for cleaned, business-ready views.

### 2. Pipeline Configuration
![alt text](<Screenshot 2026-04-09 at 2.38.45 PM.png>)
* Established a **Postgres-to-BigQuery** connection in Airbyte.
* Configured **Replicate Source** sync modes to maintain a mirror of the production data.
* Implemented automated schema mapping to handle data type conversions between Postgres and BigQuery.

## 📊 Sample Transformation (SQL)
*Once data is loaded, I utilize BigQuery SQL to create analytical views. For example, joining orders to customers:*

```sql
SELECT 
    c.customer_id,
    c.first_name,
    COUNT(o.order_id) as total_orders,
    SUM(oi.price) as total_spent
FROM `raw_dataset.customers` c
JOIN `raw_dataset.orders` o ON c.customer_id = o.customer_id
JOIN `raw_dataset.order_items` oi ON o.order_id = oi.order_id
GROUP BY 1, 2;