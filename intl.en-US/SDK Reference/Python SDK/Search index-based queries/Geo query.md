# Geo query

Geo query includes geo-distance query, geo-bounding box query, and geo-polygon query.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Python SDK/Search index-based queries/Create search indexes.md).

## Geo-distance query

You can use geo-distance query to specify a circular geographical area consisting of a central point and radius as a filtering condition in a query. Tablestore returns the rows where the values of a specified column are within the circular geographical area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |field\_name|The name of the column used to match the query conditions. The specified column must be a GEOPOINT column.|
    |center\_point|The location of the central point. The value of this parameter is of the GEOPOINT data type in the following format:`latitude,longitude`. Valid values of latitude: \[-90,+90\]. Valid values of longitude: \[-180,+180\]. Example: `35.8,-45.91`. |
    |distance|The radius of the circular geographical area. The value of this parameter is of the DOUBLE data type. Unit: meters.|
    |query|The query statement for the search index. The query type. To use geo-distance query, set this parameter to GeoDistanceQuery.|
    |table\_name|The name of the table.|
    |index\_name|The name of the search index.|

-   Examples

    The following code provides an example on how to query rows where the values of the g column are within the circular geographical area whose central point is '32.5,116.5' and radius is 300,000 meters:

    ```
    query = GeoDistanceQuery('g', '32.5,116.5', 300000)
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


## Geo-bounding box query

You can use geo-distance query to specify a rectangular geographical area. Tablestore returns the rows where the values of a specified column are within the rectangular geographical area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |field\_name|The name of the column used to match the query conditions. The specified column must be a GEOPOINT column.|
    |top\_left|The location of the top-left corner of the rectangle box.|
    |bottom\_right|The location of the bottom-right corner of the rectangular box. A rectangular geographical area can be specified by a top-left corner and a bottom-right corner.The value of this parameter is in the following format: `latitude,longitude`. Valid values of latitude: \[-90,+90\]. Valid values of longitude: \[-180,+180\]. Example: `35.8,-45.91`. |
    |query|The query statement for the search index. The query type. To use geo-bounding box query, set this parameter to GeoBoundingBoxQuery.|
    |table\_name|The name of the table.|
    |index\_name|The name of the search index.|

-   Examples

    The following code provides an example on how to query rows where the values of the g column are within the rectangular geographical area whose top-left corner is at '30.9,112.0' and bottom-right corner at '30.2,119.0'.

    ```
    query = GeoBoundingBoxQuery('g', '30.9,112.0', '30.2,119.0')
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


## Geo-polygon query

You can use geo-polygon query to specify a polygonal geographical area. Tablestore returns the rows where the values of a specified column are within the polygonal geographical area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |field\_name|The name of the column used to match the query conditions. The specified column must be a GEOPOINT column.|
    |points|The coordinates of the points that compose the polygon. You can specify a polygon by using multiple coordinates in the following format:`latitude,longitude`.Valid values of latitude: \[-90,+90\]. Valid values of longitude: \[-180,+180\]. Example: `35.8,-45.91`. |
    |query|The query statement for the search index. The query type. To use geo-polygon query, set this parameter to GeoPolygonQuery.|
    |table\_name|The name of the table.|
    |index\_name|The name of the search index.|

-   Examples

    The following code provides an example on how to query rows where the values of the g column are within the polygonal geographical area that consists of the coordinates of '30.9,112.0', '30.5,115.0', '30.3, 117.0', and '30.2,119.0':

    ```
    query = GeoPolygonQuery('g', ['30.9,112.0', '30.5,115.0', '30.3, 117.0', '30.2,119.0'])
    rows, next_token, total_count, is_all_succeed = client.search(
        table_name, index_name, 
        SearchQuery(query, limit=100, get_total_count=True), 
        ColumnsToGet(return_type=ColumnReturnType.ALL)
    )
    ```


