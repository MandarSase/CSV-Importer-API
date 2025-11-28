
---

# CSV Importer API

A **FastAPI-based application** to import CSV files into a PostgreSQL database.
Each row in the CSV becomes a database record, with support for **nested fields** and **JSON storage** for complex or additional information.

---

## Features

* Convert CSV rows to JSON objects with nested support.
* Map mandatory fields (`name.firstName`, `name.lastName`, `age`) to database columns.
* Store other fields in `additional_info` as JSON.
* Batch insertion for high-performance with large CSVs (>50,000 records).
* Interactive Swagger API documentation.
* Calculates age distribution of imported users.

---

## Prerequisites

* Python 3.12+
* PostgreSQL 15+ (or latest)
* pgAdmin 4 (optional for database management)

---

## Project Structure

```
csv_importer/
│
├── main.py                 
├── .env                    #the file format is given in point 4.PS:Never share your .env file 
├── requirements.txt       
├── example/
│   └── data/
│       └── users.csv       # CSV file to import
├── app/
│   ├── config.py           
│   ├── database.py         
│   ├── routes.py          
│   ├── service.py          
│   └── csv_reader.py       
└── README.md
```

---

## Setup Instructions

### 1. Install PostgreSQL

1. Download: [PostgreSQL Windows Installer](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)
2. Install with default port `5432`.
3. Set a password for `postgres` and remember it.
4. Install **pgAdmin** for easier database management.

---

### 2. Create Database & Table
* server->PostgreSQL->Database->yourfilename->Schemas->public->Tables(right click query tools)

Using pgAdmin or SQL query:

```sql
CREATE DATABASE csv_import;

CREATE TABLE public.users (
    id SERIAL PRIMARY KEY,
    "name" VARCHAR NOT NULL,
    age INT NOT NULL,
    address JSONB,
    additional_info JSONB
);
```

---

### 3. Clone Project & Setup Python Environment

```bash
git clone <your-repo-url>
cd csv_importer
python -m venv venv
# Windows
venv\Scripts\activate
# Linux/macOS
source venv/bin/activate
pip install -r requirements.txt
```

---

### 4. Configure Environment Variables

Create `.env` in project root:

```env
# PostgreSQL config
PG_HOST=localhost
PG_PORT=5432
PG_USER=postgres
PG_PASSWORD=YourPostgresPassword   #This is asked while installing the postgre 
PG_DATABASE=csv_import

# CSV config
CSV_DIR=./example/data    #This gives the path of the csv file so place it properly
CSV_FILE=users.csv
BATCH_SIZE=1000

# FastAPI server
PORT=8000
```

> Replace `YourPostgresPassword` with your actual PostgreSQL password.

---

### 5. Prepare CSV File

* Place CSV in `example/data/`
* Example:

```
name.firstName,name.lastName,age,address.line1,address.line2,address.city,address.state,gender,email
Rohit,Prasad,35,A-563 Rakshak Society,New Pune Road,Pune,Maharashtra,male,rohit@example.com
Sneha,Kulkarni,28,B-12 Green Park,Road No 5,Mumbai,Maharashtra,female,sneha@example.com
```

* Nested fields (e.g., `address.line1`) are automatically converted to JSON objects.

---

### 6. Run FastAPI Server

```bash
uvicorn main:app --reload --port 8000
```

Access Swagger UI:

```
http://127.0.0.1:8000/docs
```

---

### 7. Import CSV

1. Go to **POST /api/import** in Swagger UI.
2. Click **Try it out → Execute**
3. Response example:

```json
{
  "message": "CSV imported successfully! Total records: 2"
}
```

---

### 8. Verify Data in PostgreSQL

* Open **pgAdmin** → Database `csv_import` → Table `users` → View/Edit All Rows
* Columns:

  * `name` (firstName + lastName)
  * `age`
  * `address` (JSON)
  * `additional_info` (JSON)

---

### 9. Optional: Age Distribution Report

```python
cur.execute("SELECT age FROM users")
ages = [row[0] for row in cur.fetchall()]

distribution = {
    "<20": len([a for a in ages if a < 20]),
    "20-40": len([a for a in ages if 20 <= a <= 40]),
    "40-60": len([a for a in ages if 40 < a <= 60]),
    ">60": len([a for a in ages if a > 60])
}

print(distribution)
```

---

## Notes

* CSV files are **read-only**; original file remains in `example/data/`.
* Supports **nested fields of any depth**.
* Large CSV files are efficiently imported in batches.
* Ensure `.env` matches PostgreSQL credentials exactly to avoid connection errors.



---
<img width="2132" height="1188" alt="image" src="https://github.com/user-attachments/assets/4ad85dd3-af1d-4c9d-8b78-ebfd4fccdf88" />

* Sample CSV file .
<img width="2156" height="344" alt="image" src="https://github.com/user-attachments/assets/28ef53ea-f41a-4a02-bdff-50f91742d30d" />

---
* Final output will be a JSON ,we can view it in pgadmin4 

* path is server->PostgreSQL->Database->yourfilename->Schemas->public->Tables->users(Right click the user and view All row)

<img width="2209" height="426" alt="image" src="https://github.com/user-attachments/assets/bb3eea1e-0abc-4712-b3e6-c16eb1f95a48" />
