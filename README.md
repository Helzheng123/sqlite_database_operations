# sqlite_database_operations

### 1. Selected Datasets
The first dataset I used was a standard charge [Excel file](https://github.com/Helzheng123/sqlite_database_operations/blob/main/datasets/1043279565_st.-francis-hospital_standardcharges.xlsx) from St.Francis Hospital. This dataset contains Billing/Charge Code, Charge Description,	Rev Code,	CPT/HCPC,	Price, NDC,	Package Size,	Package Unit,	Package Description, and Charge Quantity. The second dataset I used was a standard charge [JSON file](https://github.com/Helzheng123/sqlite_database_operations/blob/main/datasets/133957095_NewYorkPresbyterianHospital_standardcharges.json) from New York Presbyterian Hospital. This dataset contains multiple columns on Insurance (mostly Medicare and Medicaid) along with Code (CPT/DRG),	Description, Rev Code, Inpatient/Outpatient, Gross Charges, Discounted Cash Price, Minimum Negotiated Charge, and	Maximum Negotiated Charge.
### 2. Exploratory Data Analysis Process
 - Loaded Packages into the Colab notebook such as pandas and matplotlib.pyplot
 - Imported datasets
 - Checked for missing values with ```missing_values = df_<dataset name>.isnull().sum()```
 - Dropped any missing values with ```df = df_<dataset name>.dropna()```
 - Descriptive Statistics with ```df.describe()```
 - Checked Frequency counts for categorical data with ```<name with frequency count> = df['<categorical column name>'].value_counts()```
 - Descriptive Statistics (mean, median, mode, range, variance, standard deviation, IQR) for numerical data with ```mean_values_sf = dfsf[['Billing/Charge Code', 'Rev Code', 'Price', 'Package Size']].mean()``` (This is an example for the mean)
 - Created a Histogram to show data distribution with matplotlib.pyplot
### 3. Instructions on Replication of the SQLite Database setup
 - Import sqlite3 and create_engine from sqlalchemy
```
from sqlalchemy import create_engine
import sqlite3
```
 - Connect to the SQLite database and name the database ```health.db```
```
conn = sqlite3.connect('health.db')
cursor = conn.cursor()
```
#### Manually creating a table
 - Create a table named ```sfh``` (representing St. Francis Hospital)
```
cursor.execute("""
  CREATE TABLE IF NOT EXISTS sfh
              (
                  [Charge Description], text
                  [Package Size] float,
                  [Price] float,
                  [Charge Quantity] text,
                  [Package Description] text
              )
           """)
conn.commit()
```
 - Confirm if the table you created exists
```
cursor.execute("""
            SELECT name
            FROM sqlite_master
            WHERE type='table'
            ORDER BY name;
          """)

print(cursor.fetchall())
```
 - Insert values into the table ```sfh```
```
cursor.execute('''
    INSERT INTO sfh
    VALUES
        ('XRX Ciprofloxacin hcl 0.3% OP solution', 5.0, 272.00, '5 mL', 'Bottle'),
        ('XRX Cisatracurium Besylate (PF) 200 mg/20 mL IV solution', 20.0, 1964.00, '20 mL', 'Vial')
''')
conn.commit()
```
 - Display the values in the table
```
cursor.execute('''
  SELECT * FROM sfh;
''')

print(cursor.fetchall())
```
 - Then connect the table with the SQL database using sqlchemy's engine
```
engine = create_engine('sqlite:///health.db')
```
 - Once connected, you can display the table you made in the database
```
sfh = pd.read_sql("select * from sfh;", conn)
sfh
```
#### Automatically create a table
 - Load the dataset you want to display (I used the St. Francis Hospital one)
 - Push the dataframe into a SQL table
```
df_stfrancis.to_sql('stfrancis', conn, if_exists='replace')
```
 - Check/look for the table and create a new dataframe called **response**
```
query = '''
  SELECT *
  FROM stfrancis
  WHERE "Rev Code" = '301'
  LIMIT 30;
'''

response = pd.read_sql(query, conn)
response
```
Thats it! :smile:
