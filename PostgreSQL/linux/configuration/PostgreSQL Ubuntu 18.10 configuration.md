# PostgreSQL ODBC Linux (Ubuntu Server 18.10 x86_64) configuration
#### with python3 ODBC use
___

### Linux ODBC configuration

I recommend installing database ODBC driver before driver manager, therefore PostgreSQL's driver in Ubuntu Server 18.10 is installed with:

`apt install odbc-postgresql`

To properly configure ODBC on linux, driver manager is required. For the sake of this guide I have chosen unixODBC since it is available in repositories and installs odbcinst automatically if absent. On Ubuntu Server 18.10 unixODBC can be installed with:

`apt install unixodbc`

**odbcinst** is a tool for managing database connections' configuration located in odbc.ini and odbcinst.ini files.

**odbc.ini** file contains specific DSNs' configurations and **odbcinst.ini** holds information about currently installed (slash detected) _drivers for database connections_. Upon odbcinst's installation it should automatically create those files and fill odbcinst.ini with detected drivers. If no driver was found it is possible to add it manually or through odbcinst (see man page).

Sample /etc/odbcinst.ini for PostgreSQL ODBC:
```
[PostgreSQL ANSI]
Description = PostgreSQL ODBC driver (ANSI version)
Driver = psqlodbca.so
Setup = libodbcpsqlS.so
Debug = 0
CommLog = 1
UsageCount = 1

[PostgreSQL Unicode]
Description = PostgreSQL ODBC driver (Unicode version)
Driver = psqlodbcw.so
Setup = libodbcpsqlS.so
Debug = 0
CommLog = 1
UsageCount = 1
```
Configuration shown above was generated automatically after installing unixODBC.

Sample /etc/odbc.ini for PostgreSQL based Data Store:
```
[PostgreSQL30]
Driver = PostgreSQL Unicode
Description = PostgreSQL 11.1 on Windows DSN
DSN = PostgreSQL30
Servername = 192.168.0.1
Port = 5432
Servertype = postgres
Database = postgres
Trace = 1
TraceFile = /var/pg/odbc_PostgreSQL30.trace
Debug = 1
DebugFile = /var/pg/odbc_PostgreSQL30.debug
```
This example will not be generated automatically, any configuration in odbc.ini must be written by hand (see unixODBC second link in bibliography). DSN and configuration section name (_[PostgreSQL30]_) were chosen at random (after DSN name generated on windows) so they can be anything else. Username and Password can be specified in this file, but I do not support nor recommend this kind of policy.

>This ODBC configuration was pointed to **windows machine with PostgreSQL 11.1** installed on it. This is an important information, since **I am not aware whether there would be any complications if linux machine was of x32 architecture** as _Windows x86_32 is not supported for PostgreSQL 11_ (at this moment).


### Python3 ODBC with pyodbc

Firstly, header file `SQL.h` is needed for pyodbc module installation, and this requirement can be fulfilled with installation of unixodbc development files:

`apt install unixodbc-dev`

Python3 and pip3 will be required for this step:

`apt install python3 pip3`

> Important note: python's architecture MUST match database's architecture (see previous section's note), otherwise application architecture mismatch error will occur upon connection trial. To check python3's architecture just run this command:
`python -c "import platform; print(platform.architecture()[0])"`

After that, only pyodbc module is needed:
`pip3 install pyodbc`

To check whether everything is OK you can use this short code (filling UID and PWD with proper data):
```
import pyodbc
conn = pyodbc.connect('DSN=[PostgreSQL30];UID=postgres;PWD=password')
print(conn.getinfo(odbc.SQL_DATABASE_NAME))
```

## Bibliography

| Technology | Links |
| - | - |
| unixODBC | http://www.unixodbc.org/ | 
| | http://www.unixodbc.org/odbcinst.html |
| iODBC/libidobc | http://www.iodbc.org/ |
| ODBC driver managers | https://www.easysoft.com/developer/interfaces/odbc/linux.html#odbc_driver_managers |
| pyodbc | https://github.com/mkleehammer/pyodbc/wiki |
| | https://github.com/mkleehammer/pyodbc/wiki/Drivers-and-Driver-Managers |
| Windows PostgreSQL ODBC drivers | https://www.postgresql.org/ftp/odbc/versions/ |
| SqlDriverConnect | https://docs.microsoft.com/en-us/sql/odbc/reference/syntax/sqldriverconnect-function?view=sql-server-2017 |

I highly recommend reading pyodbc wiki and easysoft article, as they contain lots of good informations about linux ODBC and managers.

##### Guide created with help of:
https://dillinger.io/
