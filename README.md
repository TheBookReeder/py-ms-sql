# Microsoft SQL Server Easy Connect
The purpose of this small utility, one class module is to make connecting to a MS SQL Server easier. One problem I experienced through putting python code in production is: the operating system makes a difference. So if one has a error-free project running on their Windows machine and then deploy it on a unix based server, errors appear. This modul is OS independant (with one caveat, see below) so that regardless of where you the project is deployed, it will run without errors. 

### Quick Start
```
$ pip install py_ms_sql
```

### Usage
```python
from py_ms_sql import py_ms_sql

# Set up connection object (OPTIONAL: pass a logger object)
sql_conn = py_ms_sql.ConnectSQL()

# Use one of the following (in order of ease-of-use)
# DSN of ODBC
state, msg = sql_conn.connect(dsn='mysqldsn', uid='user', pwd='password')

# OR
# ODBC Driver String
state, msg = sql_conn.connect(driver_string="{SQL Server};SERVER=server_name;" \
                               "DATABASE=my_db;UID=user;PWD=password")

# OR
# ODBC Driver Details
state, msg = sql_conn.connect(driver='{SQL Server}',server='server_name',
                 db='my_db',uid='user',pwd='password')

# OR
# Specify ODBC Driver Location
state, msg = sql_conn.connect(host="my-server.com",port=1433,db="my_db",
                 uid='user',pwd=password,tds_version=7.3
                 driver='/usr/local/lib/libtdsodbc.so')

# Run a SELECT statement query (return error status and Pandas DataFrame)
state, data = sql_conn.select(sql_query)

# OR
# Run an INSERT statement query (expects a table name and pandas DataFrame)
state, msg = sql_conn.insert_df(df, table)

# Close connection
state, msg = sql_conn.close_conn()
```

Run any one of the above commands and if the code can find the ODBC driver, it will connect.

**NOTE:** The `.query()` method returns a Pandas DataFrame 

### Self Debug
In the event of errors try the following:
1. Check, double check and check your connection details one more time.
1. If you're connecting from something other than a Microsoft Machine, you may need to use the last option since some MS Sql Server use instance name to have a discoverable port number, and non-Microsoft machines struggle to discover port number (sometimes)
1. If using FreeTDS on a non-Microsoft machine, make sure it is installed properly 

### TODO
- [x] allow for *writing* to MS SQL Server (if incomplete, only allows for read queries)
- [ ] improve ease of portability so that no effort (what-so-ever) is needed to accomodate operating system
- [ ] allow for non-Pandas DataFrame returns

### Fine Print
The main reason operating systems cause errors with connecting to MS SQL Server is due to the drivers and how a machine finds the drivers. For example, Windows can connect using an ODBC manager (so can MAC OSX, but not the easiest). Therefore, to make sure your deployment doesn't run with errors, use `os.name` to determine which OS is running and call the correct driver/use the appropriate connection details.
