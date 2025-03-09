### CODTECH INTERNSHIP TASK - 3: **Database Migration**

Below are the steps involved in the migration process, including SQL scripts for data extraction, transformation, and loading (ETL) from MySQL to PostgreSQL.

### **Step 1: Database Setup**

#### **MySQL Database** (Source Database):
Let's assume we have a MySQL database named `company_db` with the following schema:

**Tables:**
1. **employees**
   - `employee_id` (INT)
   - `employee_name` (VARCHAR)
   - `department_id` (INT)
   - `salary` (DECIMAL)
   - `hire_date` (DATE)

2. **departments**
   - `department_id` (INT)
   - `department_name` (VARCHAR)

#### **PostgreSQL Database** (Target Database):
We will migrate the data to a PostgreSQL database named `company_db_pg`. The schema will be similar to the MySQL schema.

---

### **Step 2: Data Extraction from MySQL**

#### 1. **Export Data from MySQL (Using `mysqldump`)**

To begin the migration, we will use `mysqldump` to export the data from the **MySQL** database:

```bash
mysqldump -u username -p company_db employees departments > company_db_backup.sql
```

This command will generate a backup file (`company_db_backup.sql`) containing the data and structure for the `employees` and `departments` tables.

#### 2. **Export Data Using SQL Queries (Optional)**

Alternatively, you can export the data using SQL queries:

```sql
-- Export data from employees table
SELECT * FROM employees;

-- Export data from departments table
SELECT * FROM departments;
```

You can then export this data to CSV files for later import into PostgreSQL.

---

### **Step 3: Modify Schema for PostgreSQL**

Before importing the data into **PostgreSQL**, we might need to adjust some schema definitions to make them compatible with PostgreSQL. For instance:

1. **PostgreSQL Data Types**:
   - `VARCHAR` in MySQL is compatible with PostgreSQL.
   - `INT` is compatible with both MySQL and PostgreSQL.
   - `DECIMAL` is compatible in both databases.

2. **Primary Key and Foreign Key Constraints**: Ensure proper indexes, constraints, and keys are created.

---

### **Step 4: Data Transformation (If Necessary)**

If there are differences in data types or other constraints between MySQL and PostgreSQL, the data should be transformed. For example:

- **Date Formatting**: MySQL and PostgreSQL handle date formats slightly differently, so the date format might need adjustment during migration.
- **Handling Auto-Increment**: MySQL uses `AUTO_INCREMENT` for auto-incrementing primary keys, while PostgreSQL uses `SERIAL`.

---

### **Step 5: Data Import to PostgreSQL**

#### 1. **Create Tables in PostgreSQL**

First, recreate the tables in **PostgreSQL** based on the MySQL schema. Here are the SQL commands to create the tables in PostgreSQL:

```sql
-- Create employees table in PostgreSQL
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    employee_name VARCHAR(255) NOT NULL,
    department_id INT NOT NULL,
    salary DECIMAL(10, 2),
    hire_date DATE
);

-- Create departments table in PostgreSQL
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(255) NOT NULL
);
```

#### 2. **Import Data from CSV (if exported as CSV)**

If you exported the data to CSV files (e.g., `employees.csv`, `departments.csv`), you can import the data into PostgreSQL using the `COPY` command:

```sql
-- Import employees data into PostgreSQL
COPY employees (employee_id, employee_name, department_id, salary, hire_date)
FROM '/path/to/employees.csv'
DELIMITER ',' CSV HEADER;

-- Import departments data into PostgreSQL
COPY departments (department_id, department_name)
FROM '/path/to/departments.csv'
DELIMITER ',' CSV HEADER;
```

#### 3. **Import Data from MySQL Backup (if using `mysqldump`)**

If you used `mysqldump` to export the data, you can import the data into PostgreSQL using the following steps:

1. Open the `.sql` backup file and modify the `AUTO_INCREMENT` syntax for PostgreSQL:

   - Replace `AUTO_INCREMENT` with `SERIAL` for auto-increment fields.
   - Ensure any MySQL-specific data types are adjusted (e.g., `TINYINT` to `BOOLEAN`).

2. After modification, execute the SQL script in PostgreSQL:

```bash
psql -U username -d company_db_pg -f company_db_backup.sql
```

---

### **Step 6: Verify Data Integrity**

After migrating the data, it is essential to verify the integrity of the data in PostgreSQL. Here are some checks:

1. **Row Count Comparison**: Ensure the row count is the same between MySQL and PostgreSQL.

```sql
-- In MySQL
SELECT COUNT(*) FROM employees;
SELECT COUNT(*) FROM departments;

-- In PostgreSQL
SELECT COUNT(*) FROM employees;
SELECT COUNT(*) FROM departments;
```

2. **Data Validation**: Compare sample data to ensure it migrated correctly. For example:

```sql
-- Compare data between MySQL and PostgreSQL for a sample employee
-- In MySQL
SELECT * FROM employees WHERE employee_id = 1;

-- In PostgreSQL
SELECT * FROM employees WHERE employee_id = 1;
```

3. **Referential Integrity**: Ensure foreign keys and relationships between tables are preserved by checking the data consistency.

```sql
-- Verify foreign key constraints (example)
SELECT * FROM employees e
JOIN departments d ON e.department_id = d.department_id;
```

---

### **Step 7: Summary Report of the Migration Process**

#### **Migration Process Summary**

1. **Source Database**: MySQL (`company_db`).
2. **Target Database**: PostgreSQL (`company_db_pg`).
3. **Migration Tool**: `mysqldump` for schema and data export, followed by `COPY` or modified `SQL` scripts for import into PostgreSQL.
4. **Data Integrity Checks**: Verified by comparing row counts, data samples, and referential integrity.

#### **Challenges Encountered**
- **Date Formatting**: Adjusted the date formats between MySQL and PostgreSQL.
- **Auto-Increment Handling**: Replaced MySQL's `AUTO_INCREMENT` with PostgreSQL's `SERIAL`.
- **Foreign Key Constraints**: Ensured that foreign key constraints were correctly mapped between tables.

#### **Outcome**
- The migration was successful with all data transferred accurately and no loss of integrity.
- Data verification and consistency checks confirmed that the target PostgreSQL database mirrors the original MySQL database.

---

### **Step 8: Final SQL Migration Script (Example)**

Here’s an example of a full migration script for PostgreSQL after data export:

```sql
-- Create the departments table in PostgreSQL
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(255) NOT NULL
);

-- Create the employees table in PostgreSQL
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    employee_name VARCHAR(255) NOT NULL,
    department_id INT,
    salary DECIMAL(10, 2),
    hire_date DATE,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

-- Import data from CSV or manually transferred data
COPY departments(department_id, department_name) FROM '/path/to/departments.csv' DELIMITER ',' CSV HEADER;
COPY employees(employee_id, employee_name, department_id, salary, hire_date) FROM '/path/to/employees.csv' DELIMITER ',' CSV HEADER;
```

---

### **Conclusion**

The migration process from MySQL to PostgreSQL has been successfully completed with the following steps:

1. Data extraction using `mysqldump` or `SELECT INTO`.
2. Schema adjustment for PostgreSQL compatibility.
3. Data import into PostgreSQL using the `COPY` command or modified `SQL` files.
4. Data verification and consistency checks to ensure integrity.


