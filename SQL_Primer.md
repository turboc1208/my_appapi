
<p>SQL is a standard syntax for talking to relational databases
like Access, MySQL, SQLite, Oracle, SQL Server, etc. There are some differences from platform
to platform, but for the most part it is very transportable between
platforms. That is part of what
makes it such a great tool. </p>

<p></p>

<p>A database is a fancy way of saying a group of one or more data
files tied together by links. Using
a broad definition of database, the Web is a database. It has information and there are hyperlinks
that relates the information. A
database can be built using a database using spreadsheets like Excel. In excel a formula in a cell can link to
other cells either on the same sheet, in the same workbook, or even other
workbooks. Think about a mailing
list in word that pulls names and address from a text file. ThatÕs another example of a
database. These examples work fine
when you are dealing with a couple of hundred rows like maybe an invitation
list for a wedding. Now imagine the
records your city maintains for driverÕs registration. There are tens to hundreds of thousands
of names in that list. That would
be nearly impossible to maintain in a spreadsheet. At the very least, it would be very
slow. </p>

<p></p>

<p>This is where databases come in. Databases provide an optimized way to
deal with large quantities of related data in a fast, efficient manner. But at their most basic, they are just a
spreadsheet or a mailing list.</p>

<p></p>

<p>To get the information out of a database, we need a way to
ask the database for the information we want. That language is called Structured Query
Language or SQL. SQL statements can
be as simple or complex as needed. </p>

<p></p>

<p>Databases use Tables to store data. Tables have rows of data in them much
like a spreadsheet has rows of data in it.
Each row of data in a table is related to itself. There might be a row with my name, driverÕs
license and address on it. Another
row might have your name, driverÕs license and address on it. For each person, we capture the same
information in the table. A
database can have one or more table.
</p>

<p></p>

<p>Let's think about home automation for a minute. With home automation, we are concerned
with two basic things. The first is
our house and the rooms in the house.
The second is the devices. Each
of these things are completely different and can function independently of each
other. So lets try to describe this in a database by creating two
tables.</p>

<p></p>

<p><pre> 
DEVICE                   HOUSE
-----------------------  ---------------
HA_NAME                  ROOM
USE_TYPE                 HOUSE_NAME
ON_STATE                 OCCUPANT
OFF_STATE
ON_BRIGHTNESS
OFF_BRIGHTNESS
MAX_BRIGHTNESS
ROOM
</pre><p>
First understand that I made up these tables and names to illustrate a point. These ae not necessarily the way tables should be setup in a good database.  However, they will work to illustrate the points in this document.

<p></p>

<p>The DEVICE table holds information about each device we are interested in controlling. If your app is not controlling some of the devices, in your house, those devices do not need to be in the table. <p>
<p>
HA_NAME - the HomeAssistant name for the device (light.office_lights, media_player.den_directv, device_tracker.sam,etc). We will use this column as the key column to access this table. A good key column should be unique and by definition, the HomeAssistant name of a device is unique.</p>

<p></p>

USE_TYPE - This is a field that describes how the device is used. This does not necessarily match the HomeAssistant device type. For example, light.den_ceiling_fan, is a homeassistant light (a device that is dimmable). But we are using the light switch to control a ceiling fan. So the USE_TYPE could be FAN. Whereas light.den_ceiling_fan_light is a light kit mounted on the ceiling fan and controlled separately. This allows us to figure out what light.ceiling_fan_light is without having to parse the name and guess what it is.</p>

<p></p>

ON_STATE - Some devices indicate they are on by setting their state to ÒonÓ. Others like the media player in my den, use 'playing' to indicate they are on.  When the door to my office is open, itÕs state is 23. This gives us a way to know what to check for when we check the state on a device.</p>

<p></p>

OFF_STATE - Like ON_STATE just tracks the values associated with OFF for each device.</p>

<p></p>

ON_BRIGHTNESS - When a light or fan is turned on, what brightness should be used.</p>

<p></p>

OFF_BRIGHTNESS - When a light or fan is turned off, what brightness should be used. We have one room in our house that is
always hot so we like to keep the ceiling fan running at half speed even when
no one is home. So the off_brightness for this fan can be set to half itÕs
max_brightness.</p>

<p></p>

MAX_BRIGHTNESS - what is the value associated with max brightness for this device.</p>

<p></p>

ROOM - what room is the device installed in. This should match one of the room names in the ROOM table.</p>

<p><pre>  </pre></p>

The HOUSE table holds the information about our house and the rooms in the house. </p>

<p></p>

ROOM - This
is the name of the room where a device may be installed. This is the unique key on this table. </p>

<p></p>

HOUSE_NAME - this allows us to track more than one property. You could track devices at your primary home and at rental property.</p>

<p></p>

OCCUPANT - This is the name or maybe a device_tracker from the device table for the person that primarily occupies the room. This way when that person leaves home, we can turn off all devices in their room.  If the room is a family room with no clear owner, this could be Everyone, or just left blank.</p>

<p></p>

We use the db_create_table function to create these tables.</p>

<p></p>

We then use the db_insert function to insert rows into each table.</p>

<p></p>

We can also update the data in the tables using db_update or delete rows if someone gets un-invited using db_delete.</p>

<p></p>

Finally the part everyone has been waiting for, we can use SQL to ask the database for information. SQL uses a ÒselectÓ statement to ask for rows of data. We use the db_select function.</p>

<p></p>

A SQL statement to get a list of devices might look something like this.</p>

<p></p>

<pre> SELECT HA_NAME, USE_TYPE FROM DEVICE </pre></p>

<p></p>

Wow that was hard. This statement will return a row for each device in the database with their HA_NAME and USE_TYPE.</p>

<p></p>

<p>Sample output might be</p>

<p></p>

<p><pre> 
HA_NAME                      USE_TYPE
---------------------------- --------------------
light.office_fan             FAN
light.office_light           LIGHT
light.den_ceiling_fan        FAN
light.den_ceiling_fan_light  LIGHT

<p><pre> media_player.office </pre>_directv MEDIA</p>

<p><pre> media_player.office </pre>_tv<pre> 
 </pre>MEDIA</p>

<p><pre> media_player.den_tv </pre><pre> 
 </pre>MEDIA</p>

<p><pre> media_player.den_apple_tv </pre><pre>   </pre>MEDIA</p>

<p><pre> device_tracker.sam </pre><pre> 
 </pre>OCCUPANT</p>

<p><pre> device_tracker.chip </pre><pre> 
 </pre>OCCUPANT</p>

<p><pre> device_tracker.susan </pre><pre> 
 </pre>OCCUPANT</p>

<p><pre> light.sam_fan </pre><pre> 
 </pre>FAN</p>

<p><pre> light.sam_light </pre><pre> 
 </pre>LIGHT</p>

<p></p>

á
Note:
SQL is case insensitive, upper case or lower case letters mean the same, EXCEPT
inside quotes. If USE_TYPE is
entered in the table in all upper case ÔFANÕ, then it must be searched for in
all upper case. USE_TYPE=ÕfanÕ will not match if it was inserted into the
database in upper case. There are
modifiers that can be used to adjust case, but itÕs best to just be consistent.</p>

<p></p>

<p>Now letÕs
try something a little more difficult. LetÕs get a list of all
of the fans in the house. To
do that we introduce the ÒWHEREÓ clause.</p>

<p></p>

<p><pre> Select ha_Name </pre>, Use_Type, rOOm FrOm
dEvIce where uSe_tYpe=ÕFANÕ</p>

<p></p>

<p>This asks
the database for the name, HA_NAME, USE_TYPE and ROOM from the DEVICE table
where the USE_TYPE of that device is FAN.
The where clause is probably the most powerful part of the SQL statement
because it is where we narrow down the list to only the rows we want to see.</p>

<p></p>

<p>Sample
output might be:</p>

<p><pre> HA_NAME
 </pre>USE_TYPE
ROOM</p>

<p><pre> ----------------------
------------------------ --------------- </pre></p>

<p>light.office<pre> _fan </pre><pre>   </pre>FAN
OFFICE</p>

<p><pre> light.den_ceiling_fan </pre> FAN<pre> 
 </pre>DEN</p>

<p><pre> light.sam_fan </pre><pre> 
 </pre>FAN
SAM</p>

<p></p>

<p>Also note
in the query I made random charactersÕ upper case and lower case. The only place that case mattered was
inside the quote where I matched use_type=ÕFANÕ
because I put all the use_types in the database in
upper case.</p>

<p></p>

<p>Where
clauses can be as complicated as needed.</p>

<p></p>

<p><pre> SELECT HA_NAME,
USE_TYPE, ROOM  </pre></p>

<p><pre> WHERE USE_TYPE=ÕFANÕ
 </pre></p>

<p><b<pre> AND </pre></b><pre> 
ROOM=ÕOFFICEÕ </pre></p>

<p></p>

<p>The AND
clause is part of the where clause.
It says give me all the devices that meet these criteria. In the case of our example. Select the rows where the USE_TYPE is
FAN AND the ROOM is OFFICE.</p>

<p></p>

<p>Sample
output may be:</p>

<p></p>

<p><pre> HA_NAME
 </pre>USE_TYPE
ROOM</p>

<p><pre> ----------------------
------------------------ --------------- </pre></p>

<p>light.office<pre> _fan </pre><pre>   </pre>FAN
OFFICE</p>

<p></p>

<p>The OR
clause is like the AND clause we just saw except that instead of requiring that
both criteria be met, it requires that either the criteria on the left of it OR
the criteria on the right of it are true.</p>

<p></p>

<p><pre> SELECT HA_NAME, USE_TYPE,
ROOM  </pre></p>

<p><pre> FROM PERSON  </pre></p>

<p><pre> WHERE USE_TYPE=ÕFANÕ
OR ROOM=ÕDENÕ </pre></p>

<p></p>

<p>Sample
output may be:</p>

<p></p>

<p><pre> HA_NAME
 </pre>USE_TYPE
ROOM</p>

<p><pre> ----------------------------  </pre>-------------------- -------------</p>

<p>light.office<pre> _fan </pre><pre> 
 </pre>FAN
OFFICE</p>

<p><pre> light.den_ceiling_fan </pre><pre> 
 </pre>FAN
DEN</p>

<p><pre> light.den_ceiling_fan_light </pre><pre> 
 </pre>LIGHT
DEN</p>

<p><pre> media_player.den_tv </pre><pre> 
 </pre>MEDIA
DEN</p>

<p><pre> media_player.den_apple_tv </pre><pre>   </pre>MEDIA
DEN</p>

<p><pre> light.sam_fan </pre><pre> 
 </pre>FAN SAM</p>

<p></p>

<p></p>

<p>This gave
us all the devices that are a FAN along with everything regardless of USE_TYPE
that are in the DEN. </p>

<p></p>

<p>OR lets us
select rows that meet multiple values for the same criteria.</p>

<p>AND lets us
select rows that have the same values for multiple criteria.</p>

<p></p>

<p>AND and OR clauses can be used together. But be careful. Just like in math where there is an order
or precedence of operations. There
is one for how Where clauses process ÔANDÕ and ÔORÕ statements. The easiest way to make sure you get
what you are looking for is to use parentheses to force it. Parentheses in math and in SQL are
always evaluated first.</p>

<p></p>

<p>For example:</p>

<p></p>

<p><pre> SELECT * FROM DEVICE
WHERE ROOM=ÕDENÕ AND USE_TYPE=ÕMEDIAÕ OR USE_TYPE=ÕFANÕ </pre></p>

<p></p>

<p>What are we
really asking for here? Are we
asking for all the rows where the (room=ÔDENÕ AND the use_type=ÕMEDIAÕ) OR where the use_type=ÕFANÕ? Or are we asking for all the rows where
the room=ÕDENÕ AND the use_type is either MEDIA or
FAN? Unless you happen to know SQLÕs
order of precedence in evaluating where clauses, we donÕt know what SQL will
return. So
use parentheses to get what you want in that type of complex statement.</p>

<p></p>

<p>Another new
thing. What is column Ò*Ó. This asks SQL to return all the columns
for the rows it finds. We have been
specifying the columns we wanted to see.
With Ò*Ó we get back all the columns.</p>

<p></p>

<p></p>

<p>NOTE:</p>

<p> Database queries are not
instantaneous. There can be some
delay times associated with updates and selects especially if multiple
applications are accessing the same database at the same time. These are database locks and are a
normal part of database processing.
Normally locks only happen for a second or two if at all. But because there can be delays it is
recommended that you do not query the database as part of a time critical
operation. Select data from the
database during quiet times in your application or setup a timer loop to
re-read the database periodically and populate a global dictionary with the
data to avoid locks impacting the response time turning on and off devices.</p>

<p></p>

<p></p>

<p>This is
probably the most complicated queries you will do starting out. If you find yourself working with
multiple tables, there are some great tutorials online that can help you with
the more advanced parts of SQL.
Hopefully this is enough to get your started.</p>

<p></p>

</div>

</body>

</html>
