# 🌍 Synapse E2E Project with Spark Pool

This project demonstrates an **end-to-end (E2E) data engineering pipeline** in **Azure Synapse Analytics (Spark Pool)**.  
The pipeline ingests raw earthquake data from the **USGS Earthquake API**, processes it through **Bronze → Silver → Gold layers**, and outputs curated datasets for analytics and reporting.

---

✅ This README has:  
- **Modern collapsible repo structure**  
- **Workflow diagram (Mermaid)**  
- **Detailed Bronze/Silver/Gold sections** with functions explained  
- **Hyperlinked images** (so screenshots show up directly in GitHub preview)  
- **End-to-End pipeline flow**  

---


## 📂 Repository Structure

<details>
<summary>📂 Root</summary>

- 🖼️ [Cancelling a job before executing another](./Cancelling%20a%20job%20before%20executing%20another.png)  
- 🖼️ [Execute all notebooks with a pipeline](./Execute%20all%20notebooks%20with%20a%20pipeline.png)  
- 🖼️ [Extracted JSON data](./Extracted%20Json%20data.png)  
- 📜 [combined_notebook.py](./combined_notebook.py)  

</details>

<details>
<summary>📂 Bronze</summary>

- 📓 [Bronze Notebook.ipynb](./Bronze/Bronze%20Notebook.ipynb)  
- 🖼️ [Bronze_notebook Spark execution](./Bronze/Bronze_notebook%20Spark%20execution.png)  

</details>

<details>
<summary>📂 Silver</summary>

- 📓 [Silver Notebook.ipynb](./Silver/Silver%20Notebook.ipynb)  

</details>

<details>
<summary>📂 Gold</summary>

- 📓 [Gold Notebook.ipynb](./Gold/Gold%20Notebook.ipynb)  

</details>

---

## ⚙️ Workflow

```mermaid
graph TD
    A[🌐 USGS Earthquake API] --> B[🥉 Bronze Layer: Raw Ingestion]
    B --> C[🥈 Silver Layer: Data Cleaning & Transformation]
    C --> D[🥇 Gold Layer: Enrichment & Aggregation]
    D --> E[📊 Analytics & Reporting]

## 🥉 Bronze Layer (Raw Data Ingestion)

**Goal:** Ingest raw earthquake data (JSON) from the **USGS API** and store it in the Bronze layer (**ADLS Gen2**).

### 🔹 Key Steps
- Mount ADLS Gen2 and define storage paths.  
- Fetch data using Python’s `requests` library.  
- Save API response to **ADLS Bronze container**.  
- Return metadata (`start_date`, ADLS paths) to pipeline using `mssparkutils.notebook.exit()`.  

### 🔹 Functions & Libraries Used
- `requests.get()` → Fetch earthquake data from API  
- `json.dumps()` → Serialize API response  
- `spark.read.json()` → Load JSON into Spark DataFrame  
- `df.write.json()` → Persist raw data in Bronze container  

📷 [Extracted JSON Data](./Extracted%20Json%20data.png)

---

## 🥈 Silver Layer (Data Transformation & Validation)

**Goal:** Transform raw Bronze data into structured Silver tables for easier querying and validation.  

### 🔹 Key Steps
- Flatten JSON fields into structured columns (longitude, latitude, elevation, title, place, magnitude, etc.).  
- Validate missing values using `when` and `isnull`.  
- Convert UNIX timestamps to proper `TimestampType`.  
- Save transformed dataset to **Parquet format** in Silver container.  

### 🔹 Functions & Libraries Used
- `col()` → Select and transform JSON fields  
- `isnull()` & `when()` → Handle missing/null values  
- `cast(TimestampType())` → Convert time fields  
- `df.write.parquet()` → Save structured Silver dataset  

---

## 🥇 Gold Layer (Enrichment & Aggregation)

**Goal:** Enrich Silver data with geospatial attributes and classify events by significance for analytics.  

### 🔹 Key Steps
- Read from **Silver dataset**.  
- Enrich data with **country codes** using `reverse_geocoder`.  
- Classify earthquake significance (`sig_class`) as **Low, Moderate, High**.  
- Save enriched dataset to **Parquet format** in Gold container.  

### 🔹 Functions & Libraries Used
- `reverse_geocoder.search()` → Get country codes from lat/lon  
- `udf()` → Register custom Python UDF for Spark  
- `withColumn()` → Add derived attributes  
- `when()` → Create significance classification rules  
- `df.write.parquet()` → Save curated Gold dataset  

---

## 📊 End-to-End Flow

1. **Bronze Notebook** → API ingestion (raw data in JSON).  
2. **Silver Notebook** → Data shaping, validation, timestamp conversion.  
3. **Gold Notebook** → Enrichment (location, classification) for analytics.  
4. **Pipeline Orchestration** → Synapse pipelines run notebooks in order.  

📷 [Execute all notebooks with a pipeline](./Execute%20all%20notebooks%20with%20a%20pipeline.png)

---

## 🚀 Key Learnings

- How to integrate **external APIs** with Synapse Spark Pools.  
- Ingest and store data in **ADLS Gen2** following **Medallion Architecture** (Bronze → Silver → Gold).  
- Use **PySpark functions** for data validation, transformation, and enrichment.  
- Orchestrate notebooks via **Azure Synapse Pipelines** for automation.  

