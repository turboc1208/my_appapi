# my_appapi
my helper functions
<P>
* Note there are other functions at the top of the file that are part of my personal toolkit.  Please feel free to remove them or keep them.  its up to you.
<p>
AppDaemon Database Tools<P>

These tools provide a wrapper around the standard sqlite3 python package.  The following functions are available in limited form.<P>
<ul>
<li>db_open – open / create database file on filesystem
<li>db_close – close currently open database
<li>db_commit – write changes to disk
<li>db_save - write changes to disk
<li>db_rollback – reverse all changes since last commit.
<li>db_undo_changes - reverse all changes since last commit.
<li>db_no_data_found – check if last query resulted in no rows returned.
<li>db_data_found - check if last query returned data.
<li>db_create_table – Create table
<li>db_query – perform non-data returning queries (insert, update, delete, etc)
<li>db_select – perform data returning queries (select)
<li>db_insert – insert one or more rows into a table using dictionary to pass data
<li>dbr_insert – insert a single row into a table using kwargs to pass data
<li>db_update – update one or more rows in a table using dictionary to pass data
<li>db_delete – delete one or more rows in table using dictionary to pass “where” criteria
<li>dbr_delete_row – delete one or more rows in table using kwargs to pass “where” criteria
</ul>


<h1>db_open(“full path database filename”)</h1>
  Opens a connectionHandle used by all other functions.  The full path to the database file is used as an argument.  If the database does not exist it is created.  <br>
Returns: ConnHandle<p>

<h1>db_close(connHandle)</h1>
   Closes the connectionHandle and unlocks the database.
<p>
<h1>db_commit(connHandle)</h1>
   Writes any uncommitted changes to the database.
<p>
<h1>db_save(connHandle)</h1>
   Writes any uncommitted changes to the database.
<p>
<h1>db_rollback(connHandle)</h1>
   Rollsback any uncommitted changes.
<p>
<h1>db_undo_changes(connHandle)</h1>
   Undo any uncommitted changes.
<p>
<h1>db_no_data_found(datadictionary)</h1>
  Return True or False depending on whether datadictionary is the result of a query that returned no rows.  The returnvalue of the db_select is passed in as datadictionary.<br>
True = no data was found in the select.<br>
False = Data was returned by the query.  Data being returned means that either more than one row was returned, or the data fields in row returned had something other than “” in at least one of them.
<p>
Example:<br>
<p><pre>
If self.db_no_data_found(self.db_select(“select ‘x’ from device where room=’office’”)):
  Self.log(“no data fould for room=office”)
Else:
  Self.log(“data found for room=office”)
  Process data
  </pre><p>
  <h1>db_data_found(datadictionary)</h1>
  Return True or False depending on whether datadictionary is the result of a query that returned one or more rows.  The returnvalue of the db_select is passed in as datadictionary.<br>
True = Data was returned by the query.  Data being returned means that either more than one row was returned, or the data fields in row returned had something other than “” in at least one of them.
False = no data was found in the select.<br>
<p>
Example:<br>
<p><pre>
If self.db_data_found(self.db_select(“select ‘x’ from device where room=’office’”)):
  Self.log(“data fould for room=office”)
  process data
Else:
  Self.log(“No data found for room=office”)
  </pre><p>


<p>
<h1>db_create_table(connHandle, TableName, Cols_Dictionary, (table_constraints def=””))</h1>
  Create a table named TableName.  Columns and column attributes are passed in the cols_Dictionary.  Table_Constraints is a string used to pass any table level constraints. 
<pre>
  Cols_Dictionary={“Column1”:”Atributes”,
                   “Column2”:”Attributes”,
                   … }
 </pre><br>
  Column names are strings.
  <ul>
      <li>Attributes : 
          <ul><li>Type : 
              <ul><li>Varchar2
                  <li>Integer
                  <li>Date</ul>
              <li>Other:
              <ul><li>Primary key   
                  <li>Primary key autoincrement   
                  <li>Can only be used with an integer type primary key
                  <li>Unique</ul>
                  </ul>
                  </ul>
 <p>
Example:
<pre>
   Conn=db_open(“/home/homeassistant/mydb.sqlite”)
   Col_dict={“Entity_id”:”integer primary key autoincrement”,
             “HA_Name”:”varchar2 unique”,
             “room”,”varchar2”}
   Db_create_table(Conn,”Entity”,Col_dict)
</pre><p>
This will create a database in the /home/homeassistant directory named mydb.sqlite.  In that database, a table will be created named Entity.  The Entity table has three columns.
<ul>
   <li>Entity_id  - integer, primary key, autoincrements on insert
   <li>HA_Name – varchar2 (string), unique values
   <li>Room – varchar2 (string)
</ul><p>

<p>
<h1>db_query(connHandle,query_string)</h1>
  Runs the specified query_statement.  The query string is a free form string.  Build your own sq lstatement.  Use this statement for non-returning sql statements, like (Insert, Update, Delete, Create Table, …).  The db_select function performs a select and returns the appropriate dataset for processing.
<p>
Return value is undefined.

<p>
<h1>db_select(connHandle, select_string)</h1>
  Runs the select_string.  The select string is a free form string.  Build your own sql statement.
<p>
   returns a list of dictionaries in the form of <p>
<pre>
[{Column1:Data row1, Column2:Data row1},
 {Column1:Data row2, Column2: Data row2}]
</pre><p>
A single row is still returned as a list of Dictionaries, there is just one dictionary.<p>
<pre>
[{column1:Data row1, Column2: Data row1}]
</pre><p>
If no data is found a single row with no data is returned:<p>
<pre>
[{column1:””, Column2:””}]
</pre><p>


<p>
<h1>db_insert(connHandle, TableName, DataDictionary)</h1>
  Insert one or more rows into tableName.  Rows are passed in the datadictionary using a list of dictionaries.
<pre>
  Example:
  rowData=[{“ha_name”:”light.office_light”,”room”:”office”},
           {“ha_name”:”switch.den_light”,”room”:”den”}]
  db_insert_row(conn,”device”,rowData)
</pre><p>
This will add two rows into table “device”.  One row will have a ha_name of “light.office_light” 
and a room of “office”.  The other row will have a ha_name of “switch.den_light” and a room of “den”.

<p>
<h1>dbr_insert(connHandle, TableName, kwargs)</h1>
  Insert one row into tablename setting the field values to the variables and associated values in kwargs.
<p>
  Example:<P>
<pre>
    Dbr_insert_row(conn,”device”,ha_name=”light.office_light”,
                                 room=”office”)
</pre><p>
This will insert a row into the “device” table, setting the ha_name field to “light.office_light” and the room field to “office”.  


<p>
<h1>db_update(connHandle,tableName,DataDict,WhereDict,(whereclause=""))</h1>
  Update rows in tablename  
   Update tablename set DataDict where wheredict<P>
   DataDict and WhereDict are dictionaries of the form <pre>{column:value,column:value}</pre><br>
   whereclause is an optional text parameter that overrides the "wheredict" and is an actual where clause (without the word "where")
  For the Datadict the column:value pairs are the set clause setting column = value.<br>
  For the wheredict, the colum:value pairs are the where clause where column=value<br>
  <br>
Both Datadict and wheredict can contain multiple fields.  In the datadict, when converted to a SQL statement, the column=value clause is separated from the next column=value pair by a ‘,’.  In the wheredict, when converted to a SQL statement, column=value combinations are separated with an AND.  Or is not supported at this time.<br>
See the seciont on "basic SQL" for help building a where clause.
<p>
Example:<br>
<pre>
Ddict={“ha_name”:”switch.my_room”,”room”:”sams”}
Wdict={“ha_name”:”switch.den_light”}
db_update(conn,”device”,ddict,wdict)
</pre>
This will update all rows with a ha_name of “switch.den_light” to have a name of “switch.my_room” and a room of “sams”.
<p>
<pre>
Ddict={“ha_name”:”switch.my_room”,”room”:”sams”}
Wdict={}
db_update(conn,"device",ddict,wdict,"ha_name='switch.den_light'")
</pre>
This would achieve the same results using the whereclause option.

<p>
<h1>db_delete(connHandle,tableName,WhereDict,(whereclause))</h1>
  Delete row from tableName where WhereDict matches.<br>
  Where dict is a column:value pair.  If multiple colum:value pairs are given, when converted to a SQL statement column:value pairs will be separated with an AND.  Or is not supported at this time.
whereclause is an optional text parameter that overrides the "wheredict" and is an actual where clause (without the word "where")
<p>
Example:<br>
<pre>
  db_delete(conn,”device”,{“ha_name”:”light.office_light”,”room”:”office”})
</pre>
This will delete the row from the “device” table where ha_name=”light.office_light” and where room=”office”
<pre>
  db_delete(conn,"device","ha_name='light.office_light' and room='office'")
</pre>
This will achieve the same result using the whereclause arguement
<p>
<h1>dbr_delete_row(connHandle,tableName,whereclause)</h1>
  Delete row from tableName matching the whereclause.<p>
Example:<br>
<pre>
  Dbr_delete_row(conn,”device”,"ha_name='light.office_light' and room='office')
</pre><p>
This will delete the row from the “device” table where ha_name=”light.office_light” and where room=”office”


