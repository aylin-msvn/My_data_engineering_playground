# 🛠️ ELT Data Engineering Pipeline with PostgreSQL and Docker

This project demonstrates a complete **Extract–Load–Transform (ELT)** data pipeline using **PostgreSQL** as both source and destination systems, orchestrated entirely within **Docker Compose**.

It is designed as a hands-on, minimal yet realistic example of modern data engineering practices—emphasizing containerization, SQL-driven workflows, and reproducibility.

---

## 📌 Overview

This pipeline simulates a real-world ETL scenario where:

-  A **source PostgreSQL** database is pre-loaded with structured tables and data via an SQL script.
-  A **custom Python-based ELT script**:
  - Connects to the source database
  - Dumps the data using `pg_dump`
  - Loads it into a **destination PostgreSQL** database using `psql`
-  Everything runs inside isolated Docker containers for portability and reproducibility.

---

## 🧱 Architecture
```text

┌────────────────────────┐          ┌───────────────────────────────┐          ┌────────────────────────────┐
│  📦 source_postgres     │         │   🐍 elt_script.py (Python)    │          | 📦 destination_postgres    │
│────────────────────────│          │───────────────────────────────│          │────────────────────────────│
│  PostgreSQL 15         │ <─────┐  │  Extracts data using pg_dump  │ ──────>  │  PostgreSQL 15             │
│  Initialized via       │       │  │  Loads data via psql client   │          │  Receives full dump        │
│  `init.sql` schema     │       └─ ┼───────────────────────────────┘          │  as ELT target             │
│  with sample data      │          │ Container runs once and exits │          │  after transformation      │
└────────────────────────┘          └───────────────────────────────┘          └────────────────────────────┘ 



.
├── docker-compose.yaml          # Docker orchestration
├── elt_script/
│   ├── Dockerfile               # Builds the ELT container
│   └── elt_script.py            # The actual ELT logic (pg_dump -> psql)
├── source_dbt_init/
│   └── init.sql                 # SQL schema and seed data for the source DB

```
## ✅ Prerequisites
	•	Docker

## 📦 Build and Run the Pipeline
### Step 1: Build and start all containers
docker compose up --build

### Optional: Run in background
docker compose up -d


This will:
	•	Spin up both PostgreSQL containers
	•	Initialize the source DB using init.sql
	•	Run the Python ELT script that dumps data from source and loads into destination

⸻


## 🧪 Verify the Results

### To check that data was loaded correctly into the destination:
docker exec -it elt-destination_postgres-1 psql -U postgres -d destination_db


### Then inside psql:
\dt         -- View tables   <br> 
SELECT COUNT(*) FROM users;
SELECT * FROM films LIMIT 5;

## 🧠 ELT Script Details

The Python script:
	•	Waits until the source database is ready using pg_isready
	•	Runs pg_dump to extract the schema + data from source_db
	•	Pipes the SQL dump into psql to load into destination_db

All environment variables, credentials, and database names are set in docker-compose.yaml for simplicity.

⸻

## ⚙️ Tech Stack
	•	Docker + Docker Compose
	•	PostgreSQL 15
	•	Python 3.10 (slim)
	•	psycopg2, pg_dump, and psql

⸻

## 📈 Data Model (Sample)

The init.sql script populates a mini relational schema for a movie platform:
	•	users: user profile data
	•	films: film metadata and ratings
	•	film_category: many-to-one film category tags
	•	actors: actor list
	•	film_actors: many-to-many actor-film mappings

All tables are loaded with realistic data for demo and testing.

⸻

## 📌 Use Cases
	•	🔄 Demonstrating ELT vs ETL in SQL-based pipelines
	•	🧪 Unit-testing data migration logic in isolated environments
	•	🧱 Prototyping data engineering workflows with containers
	•	📚 Teaching data engineering fundamentals

⸻

🧹 Cleanup

To stop and remove all containers, volumes, and networks:

docker compose down --volumes --remove-orphans
