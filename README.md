# my_appapi
my helper functions
<P>
AppDaemon Database Tools<P>

These tools provide a wrapper around the standard sqlite3 python package.  The following functions are available in limited form.<P>
<ul>
<li>db_open – open / create database file on filesystem
<li>db_close – close currently open database
<li>db_commit – write changes to disk
<li>db_rollback – reverse all changes since last commit.
<li>db_no_data_found – check if last query resulted in no rows returned.
<li>db_create_table – Create table
<li>db_query – perform non-data returning queries (insert, update, delete, etc)
<li>db_select – perform data returning queries (select)
<li>db_insert_row – insert a row into a table using dictionary to pass data
<li>dbr_insert_row – insert a row into a table using kwargs to pass data
<li>db_update_row – update a row in a table using dictionary to pass data
<li>db_delete_row – delete row in table using dictionary to pass “where” criteria
<li>dbr_delete_row – delete row in table using kwargs to pass “where” criteria
</ul>


db_open(“full path database filename”)
  Opens a connectionHandle used by all other functions.  The full path to the database file is used as an argument.  If the database does not exist it is created.  
Returns: ConnHandle

db_close(connHandle)
   Closes the connectionHandle and unlocks the database.

db_commit(connHandle)
   Writes any uncommitted changes to the database.

db_rollback(connHandle)
   Rollsback any uncommitted changes.

db_no_data_found(datadictionary)
  Return True or False depending on whether datadictionary is the result of a query that returned no rows.  The returnvalue of the db_select is passed in as datadictionary.
True = no data was found in the select
False = Data was returned by the query.  Data being returned means that either more than one row was returned, or the data fields in row returned had something other than “” in at least one of them.

Example:
If self.db_no_data_found(self.db_select(“select ‘x’ from device where room=’office’”)):
  Self.log(“no data fould for room=office”)
Else:
  Self.log(“data found for room=office”)
  Process data


db_create_table(connHandle, TableName, Cols_Dictionary,                        (table_constraints def=””))
  Create a table named TableName.  Columns and column attributes are passed in the cols_Dictionary.  Table_Constraints is a string used to pass any table level constraints.  
  Cols_Dictionary={“Column1”:”Atributes”,
                   “Column2”:”Attributes”,
                                 … }
  Column names are strings.  
  Attributes : 
      Type : 
•	Varchar2
•	Integer
•	Date
     Other:
•	Primary key   
•	Primary key autoincrement   * Can only be used with an integer type primary key
•	Unique
Example:
   Conn=db_open(“/home/homeassistant/mydb.sqlite”)
   Col_dict={“Entity_id”:”integer primary key autoincrement”,
             “HA_Name”:”varchar2 unique”,
             “room”,”varchar2”}
   Db_create_table(Conn,”Entity”,Col_dict)

This will create a database in the /home/homeassistant directory named mydb.sqlite.  In that database, a table will be created named Entity.  The Entity table has three columns.  
     Entity_id  - integer, primary key, autoincrements on insert
     HA_Name – varchar2 (string), unique values
     Room – varchar2 (string)


db_query(connHandle,query_string)
  Runs the specified query_statement.  The query string is a free form string.  Build your own sq lstatement.  Use this statement for non-returning sql statements, like (Insert, Update, Delete, Create Table, …).  The db_select function performs a select and returns the appropriate dataset for processing.

Return value is undefined.

db_select(connHandle, select_string)
  Runs the select_string.  The select string is a free form string.  Build your own sql statement.

   returns a list of dictionaries in the form of 
[{Column1:Data row1, Column2:Data row1},
 {Column1:Data row2, Column2: Data row2}]

A single row is still returned as a list of Dictionaries, there is just one dictionary.
[{column1:Data row1, Column2: Data row1}]

If no data is found a single row with no data is returned:
[{column1:””, Column2:””}]



db_insert_row(connHandle, TableName, DataDictionary)
  Insert one or more rows into tableName
  Rows are passed in the datadictionary using a list of dictionaries.

  Example:
  rowData=[{“ha_name”:”light.office_light”,”room”:”office”},
           {“ha_name”:”switch.den_light”,”room”:”den”}]
  db_insert_row(conn,”device”,rowData)

This will add two rows into table “device”.  One row will have a ha_name of “light.office_light” 
and a room of “office”.  The other row will have a ha_name of “switch.den_light” and a room of “den”.

Dbr_insert_row(connHandle, TableName, kwargs)
  Insert one row into tablename setting the field values to the variables and associated values in kwargs.

  Example:
    Dbr_insert_row(conn,”device”,ha_name=”light.office_light”,
                                 room=”office”)

This will insert a row into the “device” table, setting the ha_name field to “light.office_light” and the room field to “office”.  


db_update_row(connHandle,tableName,DataDict,WhereDict)
  Update rows in tablename  
   Update tablename set DataDict where wheredict
  DataDict and where Dict are dictionaries of the form {column:value,column:value)
  For the Datadict the column:value pairs are the set clause setting column = value.
  For the wheredict, the colum:value pairs are the where clause where column=value
Both Datadict and wheredict can contain multiple fields.  In the datadict, the column=value clause is separated from the next column=value pair by a ‘,’.  In the wheredict, column=value combinations are separated with an AND.  Or is not supported at this time.

Example:
Ddict={“ha_name”:”switch.my_room”,”room”:”sams”}
Wdict={“ha_name”:”switch.den_light”}
Db_update_for(conn,”device”,ddict,wdict)

This will update all rows with a ha_name of “switch.den_light” to have a name of “switch.my_room” and a room of “sams”.


db_delete_row(connHandle,tableName,WhereDict)
  Delete row from tableName where WhereDict matches.
  Where dict is a column:value pair.  If multiple colum:value pairs are given, column:value pairs will be separated with an AND.  Or is not supported at this time.

Example:
  Db_delete_row(conn,”device”,{“ha_name”:”light.office_light”,”room”:”office”})

This will delete the row from the “device” table where ha_name=”light.office_light” and where room=”office”

Dbr_delete_row(connHandle,tableName,kwargs)
  Delete row from tableName where kwargs contains the fields and values for the where clause.
Example:
  Dbr_delete_row(conn,”device”,ha_name=”light.office_light”, room=”office”)

This will delete the row from the “device” table where ha_name=”light.office_light” and where room=”office”


