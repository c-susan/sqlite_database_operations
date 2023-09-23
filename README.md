# sqlite_database_operations
HHA504 / Cloud Computing / Assignment 3 / Database Intro with SQLite

This repo contains my submission for HHA 504 Assignment 3. This assignment is a review on exploratory data analysis in addition to an intro into using SQLite through Jupyter Notebook. 

## This repo contains the following: 
+ A **datasets** folder containing the datasets using in the assignment.
+ **README.md**: contains a comprehensive overview of the assignment/repo.
+ **sqlite_database_operations.ipynb**: Jupyter notebook containing my responses for the assignment.

<br>

#### Overview of Datasets
The datasets are taken from St. Charles Hospital and Stony Brook University Hospital and includes information on the standard pricing of hospital services of each hospital. 

<br>

## Assigment Details: 

### **Data Exploration and Analysis**

Part 1 of the assigment focuses on basic exploratory data analysis. The columns of both datasets are cleaned to removed spaces and special charactered and changed to lower case letters. Datasets are then checked for null values and data types are also appropriately converted; the 'billing_charge_code' and 'rev_code' columns in the St. Charles Hospital dataset are changed from integer to string variables. Basics descriptive statics and frequency counts are then performed on both datasets.

### **SQLite Database Operations** 

Part 2 of the assignment focuses on SQL database operations using SQLite and SQLAlchemy. The following contains the steps to replicate my SQLite database setup.

1. Import packages: ```from sqlalchemy import create_engine``` and ```import sqlite3```. If SQLAlchemy is not installed, install it with the command ```pip install sqlalchemy```.
2. Create a temporary and local database using SQLite called "health.db" with the following:
```
conn = sqlite3.connect('health.db')
c = conn.cursor()
```
3. Create a table called 'health data' that contains information on patient's age, gender, weight, height, and temperature.
```
c.execute('''
            CREATE TABLE health_data (
              patient_id INTEGER PRIMARY KEY AUTOINCREMENT,
              name TEXT,
              age INTEGER,
              gender TEXT,
              weight_lbs REAL,
              height_cm REAL,
              temp_f REAL
            );
        ''')
conn.commit()
```
4. Confirm that the table 'health data' has be successfully created with the health.db database:
```
c.execute('''
  SELECT name
  FROM sqlite_master
  WHERE type='table';
  ''')

c.fetchall()
```
5. Us ```INSERT INTO`` to populate the 'health data' table with fake data:
```
sql_query = '''
INSERT INTO health_data
(
  'name',
  'age',
  'gender',
  'weight_lbs',
  'height_cm',
  'temp_f'
)
values
('Belle', 20, 'F', 134.4, 169.5, 98.6),
('Alice', 15, 'F', 120.2, 161.3, 97.7),
('John', 25, 'M', 200, 179, 98.5);
'''
```
6. Execute the the ```INSERT INTO``` query:
```
c.execute(sql_query)
conn.commit()
```
7. Use PANDAS to check the data is successfully inserted into the table:
```
pd.read_sql_query("select * from health_data;", conn)
```
8. Taking the St. Charles Hospital dataset, the ```to_sql``` function from PANDAS is used to put it into the SQLite database. The dataset is uploaded onto a table called "hospital_pricing":
```
df_stcharles = pd.read_csv('https://raw.githubusercontent.com/c-susan/sqlite_database_operations/main/datasets/st.-charles-hospital_standardcharges.csv')
df_stcharles.to_sql('hospital_pricing', conn, if_exists='replace')  # replaces table if table already exists
```
9. Run a query to test the table is successfully created:
```
query = '''
SELECT *
FROM hospital_pricing
WHERE rev_code = '301'
LIMIT 10;
'''

response = pd.read_sql(query, conn) # Creates a new dataframe called 'response' and contains the query from above
response
```

### **(Optional) Dive Deeper with SQL**
In Part 3, custom ```select``` SQL queries are ran to perform a rudimentary analysis on the data within the SQLite database. Queries are porformed on the "hospital_pricing" table containing the St. Charles Hospital dataset. Query 1 selects the 'rev_code' column and the sum of the price column. Results are grouped by rev_code and limited to show only 10 rows. Query 2 returns each distinct/unique rev_code present in the dataset.





