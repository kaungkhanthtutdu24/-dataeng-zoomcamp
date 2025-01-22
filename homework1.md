Homework1 Data Camp

---

```markdown
# Module 1 Homework: Docker & SQL

This repository contains the solutions for the Module 1 homework of the Data Engineering Zoomcamp 2025. The homework covers Docker, SQL, and Terraform.

---

## **Question 1: Understanding Docker First Run**

**Task:** Run the `python:3.12.8` image in interactive mode with the entrypoint `bash` and check the `pip` version.

**Solution:**

1. Run the Docker container:
   ```bash
   docker run -it --entrypoint bash python:3.12.8
   ```

2. Check the `pip` version:
   ```bash
   pip --version
   ```

**Answer:** `24.2.1`

---

## **Question 2: Understanding Docker Networking and Docker-Compose**

**Task:** Determine the `hostname` and `port` that **pgadmin** should use to connect to the Postgres database.

**Solution:**

In the `docker-compose.yaml` file:
- The Postgres service is named `db`.
- The Postgres container is exposed on port `5433` on the host, but internally it uses port `5432`.

**Answer:** `db:5432`

---

## **Prepare Postgres**

**Task:** Download the green taxi trips data and taxi zone lookup data, then load them into Postgres.

**Solution:**

1. Download the data:
   ```bash
   wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/green/green_tripdata_2019-10.csv.gz
   wget https://github.com/DataTalksClub/nyc-tlc-data/releases/download/misc/taxi_zone_lookup.csv
   ```

2. Unzip the green taxi trips data:
   ```bash
   gzip -d green_tripdata_2019-10.csv.gz
   ```

3. Use Python to load the data into Postgres:
   ```python
   import pandas as pd
   from sqlalchemy import create_engine

   # Load data
   df_trips = pd.read_csv("green_tripdata_2019-10.csv")
   df_zones = pd.read_csv("taxi_zone_lookup.csv")

   # Connect to Postgres
   engine = create_engine("postgresql://postgres:postgres@localhost:5433/ny_taxi")

   # Load data into Postgres
   df_trips.to_sql("green_trips", engine, if_exists="replace", index=False)
   df_zones.to_sql("zones", engine, if_exists="replace", index=False)
   ```

---

## **Question 3: Trip Segmentation Count**

**Task:** Count trips based on trip distance segments for October 2019.

**Solution:**

Run the following SQL query:
```sql
SELECT
    COUNT(CASE WHEN trip_distance <= 1 THEN 1 END) AS up_to_1_mile,
    COUNT(CASE WHEN trip_distance > 1 AND trip_distance <= 3 THEN 1 END) AS between_1_and_3_miles,
    COUNT(CASE WHEN trip_distance > 3 AND trip_distance <= 7 THEN 1 END) AS between_3_and_7_miles,
    COUNT(CASE WHEN trip_distance > 7 AND trip_distance <= 10 THEN 1 END) AS between_7_and_10_miles,
    COUNT(CASE WHEN trip_distance > 10 THEN 1 END) AS over_10_miles
FROM green_trips
WHERE lpep_pickup_datetime >= '2019-10-01' AND lpep_pickup_datetime < '2019-11-01';
```

**Answer:** `104,793; 202,661; 109,603; 27,678; 35,189`

---

## **Question 4: Longest Trip for Each Day**

**Task:** Find the pickup day with the longest trip distance.

**Solution:**

Run the following SQL query:
```sql
SELECT
    DATE(lpep_pickup_datetime) AS pickup_day,
    MAX(trip_distance) AS max_distance
FROM green_trips
GROUP BY pickup_day
ORDER BY max_distance DESC
LIMIT 1;
```

**Answer:** `2019-10-26`

---

## **Question 5: Three Biggest Pickup Zones**

**Task:** Find the top pickup locations with over 13,000 in `total_amount` for 2019-10-18.

**Solution:**

Run the following SQL query:
```sql
SELECT
    z."Zone" AS pickup_zone,
    SUM(g."total_amount") AS total_amount
FROM green_trips g
JOIN zones z ON g."PULocationID" = z."LocationID"
WHERE DATE(g."lpep_pickup_datetime") = '2019-10-18'
GROUP BY z."Zone"
HAVING SUM(g."total_amount") > 13000
ORDER BY total_amount DESC;
```

**Answer:** `East Harlem North, Morningside Heights`

---

## **Question 6: Largest Tip**

**Task:** Find the drop-off zone with the largest tip for passengers picked up in "East Harlem North" in October 2019.

**Solution:**

Run the following SQL query:
```sql
SELECT
    z2."Zone" AS dropoff_zone,
    MAX(g."tip_amount") AS max_tip
FROM green_trips g
JOIN zones z1 ON g."PULocationID" = z1."LocationID"
JOIN zones z2 ON g."DOLocationID" = z2."LocationID"
WHERE z1."Zone" = 'East Harlem North'
  AND DATE(g."lpep_pickup_datetime") >= '2019-10-01'
  AND DATE(g."lpep_pickup_datetime") < '2019-11-01'
GROUP BY z2."Zone"
ORDER BY max_tip DESC
LIMIT 1;
```

**Answer:** `JFK Airport`

---

## **Question 7: Terraform Workflow**

**Task:** Identify the correct sequence for the Terraform workflow.

**Solution:**

The correct sequence is:
1. `terraform init` (download provider plugins and set up backend)
2. `terraform apply -auto-approve` (generate proposed changes and auto-execute the plan)
3. `terraform destroy` (remove all resources managed by Terraform)

**Answer:** `terraform init, terraform apply -auto-approve, terraform destroy`

---

## **Repository Structure**

```
.
├── README.md                   # This file
├── green_tripdata_2019-10.csv  # Green taxi trips data
├── taxi_zone_lookup.csv        # Taxi zone lookup data
├── docker-compose.yaml         # Docker Compose file
└── scripts/                    # Directory for scripts (if any)
```

---

## **How to Run**

1. Clone this repository.
2. Ensure Docker and Docker Compose are installed.
3. Run `docker-compose up` to start the Postgres and pgAdmin services.
4. Use the provided Python script or Jupyter notebook to load the data into Postgres.
5. Run the SQL queries to answer the homework questions.

---

## **Submission**

Submitted via the [Homework Submission Form](https://courses.datatalks.club/de-zoomcamp-2025/homework/hw1).
```

---

### Steps to Post on GitHub:

1. Create a new repository on GitHub.
2. Clone the repository to your local machine:
   ```bash
   git clone https://github.com/your-username/your-repo-name.git
   ```
3. Save the above Markdown content in a file named `README.md` in the repository.
4. Add the necessary files (e.g., `docker-compose.yaml`, CSV files, scripts).
5. Commit and push the changes:
   ```bash
   git add .
   git commit -m "Add Module 1 Homework Solutions"
   git push origin main
   ```
6. Share the repository link in your homework submission.

Let me know if you need further assistance!