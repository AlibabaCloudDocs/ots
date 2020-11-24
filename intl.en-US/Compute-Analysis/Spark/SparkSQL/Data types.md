Data types 
===============================

This topic describes the mappings of field or value types among Spark, Scala, search indexes, and tables. When you use fields or values, make sure that the fields or values match each other among Spark, Scala, and Tablestore.

Basic data types 
-------------------------------------

The following table lists the supported basic data types.


|                    Data type in Spark                    | Data type in Scala | Data type in search indexes | Data type in tables |
|----------------------------------------------------------|--------------------|-----------------------------|---------------------|
| ByteType                                                 | Byte               | Long                        | Integer             |
| ShortType                                                | Short              | Long                        | Integer             |
| IntegerType                                              | Int                | Long                        | Integer             |
| LongType                                                 | Long               | Long                        | Integer             |
| FloatType                                                | Float              | Double                      | Double              |
| DoubleType                                               | Double             | Double                      | Double              |
| StringType                                               | String             | Keyword/Text                | String              |
| BinaryType                                               | Array\[Byte\]      | Binary                      | Binary              |
| BooleanType                                              | Boolean            | Boolean                     | Boolean             |
| String JSON (coordinate pairs of geographical locations) | String (JSON)      | *Geopoint*                  | String (JSON)       |





Geographical location data types 
-----------------------------------------------------

Search index supports geo query. When geo query is pushed down to the compute layer, Spark can query and analyze data based on geographical locations in addition to basic types of data.

Geo query includes the following query types: geo-distance query, geo-bounding box query, and geo-polygon query. Geo query is used in scenarios where you need to query information about the IoT devices, orders of food delivery services, locations of punch the clock, and geographical locations of express delivery orders. To use geo query, use the following methods:

* Use search index of Tablestore. For more information, see [GeoDistanceQuery](/intl.en-US/Function Introduction/Search Index/API operations/Geo-distance query.md).

  

* Use Spark SQL.

      // Specify the radius and central point for the query.
      select * from table where  val_geo = '{"centerPoint":"3,0", "distanceInMeter": 100000}' and name like 'ali%'
      // Use geo-bounding box query.
      select * from table where geo = '{"topLeft":"8,0", "bottomRight": "0,10"}' and id in { 123 , 321 }
      // Use geo-polygon query.
      select * from table where geo = '{"points":["5,0", "5,1", "6,1", "6,10"]}'

  




The following table lists the geographical location data types supported by Spark, Scala, search indexes, and tables.


|                                                 Data type in Spark                                                  | Value type in Scala | Data type in search indexes | Data type in tables |
|---------------------------------------------------------------------------------------------------------------------|---------------------|-----------------------------|---------------------|
| String JSON (the coordinate pair of the central point)                                                              | String (JSON)       | Geopoint                    | STRING (JSON)       |
| String JSON (coordinate pairs of the upper-left corner and lower-right corner in the rectangular geographical area) | String (JSON)       | Geopoint                    | STRING (JSON)       |
| String JSON (coordinate pairs of vertices in the polygon geographical area)                                         | String (JSON)       | Geopoint                    | STRING (JSON)       |


