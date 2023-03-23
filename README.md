# Import-Records-From-CSV-File-or-any-data-file-to-SQL-Server-or-any-database-system-With-Python
Import data from any data source using Python into a database such as SQL Server
#Connect to SQL Database: pip install pypyodbc
import pypyodbc as odbc 
#Clean out the data: pip install pandas
import pandas as pd
#Create your drivers variable: Go to windows odbc datasource - drivers and search for your driver
Driver = 'SQL Server'
#Assign the server name. To get this type in sql table @@ServerName
Server_Name = ADMIN
#Call the database
Database_Name = "TT"
#Step1: Import data from csv. Use the pandas library to import this data
df = pd.read_csv('Real-Time_Traffic_Incident_Reports.csv')
#Step 2: Data clean up. The columns that we want to import are traffic ID column, published date, issue reported, location, address, status & status date
#Change the content type from string to date
#these two lines of code are converting the datetime values in the 'Published Date' column of a pandas DataFrame df into a string format of 'YYYY-MM-DD HH:MM:SS', and assigning the formatted datetime values to both the 'Published Date' and 'Status Date' columns of the DataFrame df.
df['Published Date'] = pd.to_datetime(df['Published Date']).dt.strftime('%Y-%m-%d %H:%M:%S')
df['Status Date'] = pd.to_datetime(df['Published Date']).dt.strftime('%Y-%m-%d %H:%M:%S')
#deleting rows from a pandas DataFrame df
#The df.drop() method is used to drop/remove rows from the DataFrame df. The method takes a few parameters, including the index parameter which specifies the row(s) to drop.The df.query() method is used to filter the DataFrame df to only include rows where either the 'Location' or 'Status' column is null/missing. The | operator is used as a logical OR operator to join the two conditions.
#The resulting DataFrame with only the rows where 'Location' or 'Status' is not null/missing is returned as a result of the df.query() method. The index attribute is used to retrieve the index labels of the resulting DataFrame, which are then passed as a parameter to the df.drop() method to remove those rows from the original DataFrame df. The inplace=True parameter is used to specify that the changes should be made to the original DataFrame df in place, rather than returning a new DataFrame. This means that after the operation, the original DataFrame df will no longer contain the rows where either the 'Location' or 'Status' column was null/missing.
df.drop(df.query('Location.isnull() | Status.isnull()').index, inplace=True)
#Step2.2 Specify the column we want to import
columns = ['Traffic Report ID', 'Published Date', 'Issue Reported', 'Location', 'Address', 'Status', 'Status Date']
#Create anaother dataset object that will contain the records that will be exported to SQL Server
df_data = df[columns]
#When we use ODBC library to insert records to a database system, we need to provide nestless object so that means we need to convert our dataset to a list. To do that, convert df dataset to values and then to list
records = df_data.values.tolist()
#Step 3.1. Create the SQL Server connection to communicate with Python. Create a functions with 3 parameters: driver, server name, 
def connection_string(driver, server_name, database_name):
    conn_string = f"""
        DRIVER={{{driver}}};
        SERVER={server_name};
        DATABASE={database_name};
        Trust_Connection=yes;        
    """
    return conn_string
