# Geo query

Geo query includes the following query types: geo-distance query \(GeoDistanceQuery\), geo-bounding box query \(GeoBoundingBoxQuery\), and geo-polygon query \(GeoPolygonQuery\).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/NodeJS SDK/Search index-based queries/Create search indexes.md).

## Geo-distance query

You can use GeoDistanceQuery to specify a circular geographic area that is defined by a central point and a radius as a query condition. Tablestore returns the rows where the value of a field falls within the circular geographic area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |indexName|The name of the search index.|
    |query|The query statement for the search index. Sets the query type to TableStore.QueryType.GEO\_DISTANCE\_QUERY.|
    |fieldName|The column name. Type: Geopoint.|
    |centerPoint|The coordinate pair of the central point. The coordinate pair consists of latitude and longitude values.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of the longitude: \[-180,180\]. Example: "35.8,-45.91". |
    |distance|The radius of the circle centered on the specified location. Type: Double. Unit: meters.|

-   Examples

    The following code provides an example on how to search the table for rows where the value of Col\_GeoPoint falls within a specified distance from a specified central point:

    ```
    client.search({
        tableName: TABLE_NAME,
        indexName: INDEX_NAME,
        searchQuery: {
            offset: 0,
            limit: 10, // You can set limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
            query: { // Set the query type to TableStore.QueryType.GEO_DISTANCE_QUERY.
                queryType: TableStore.QueryType.GEO_DISTANCE_QUERY,
                query: {
                    fieldName: "Col_GeoPoint",
                    centerPoint: "1,1", // Specify the coordinate pair for a central point.
                    distance: 10000 // You can set the distance from the central point to a value greater than or equal to 10,000. Unit: meters.
                }
            },
            getTotalCount: true // The value of TotalCount indicates the number of rows that match the query conditions. By default, TotalCount is set to false, which indicates that the total number of matched rows is not returned. Set TotalCount to true in this example.
        },
        columnToGet: { // You can set RETURN_SPECIFIED to return the specified columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data from the table.
            returnType: TableStore.ColumnReturnType.RETURN_ALL
        }
    }, function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }
        console.log('success:', JSON.stringify(data, null, 2));
    });
    ```


## Geo-bounding box query

You can use geo-bounding box query to query data that falls within a rectangular geographic area. You can specify the rectangular geographic area as a query condition. Tablestore returns the rows where the value of a field falls within the rectangular geographic area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |indexName|The name of the search index.|
    |query|The query statement for the search index. Sets the query type to TableStore.QueryType.GEO\_BOUNDING\_BOX\_QUERY.|
    |fieldName|The column name. Type: Geopoint.|
    |topLeft|The coordinate pair of the upper-left corner of the rectangular geographic area.|
    |bottomRight|The coordinate pair of the lower-right corner of the rectangular geographic area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of the longitude: \[-180,180\]. Example: "35.8,-45.91". |

-   Examples

    The data type of Col\_GeoPoint is GeoPoint. The coordinate pair of the upper-left corner is "10,0". The coordinate pair of the lower-right corner is "0,10". The following code provides an example on how to obtain the rows where the value of Col\_GeoPoint falls within the rectangular geographic area:

    ```
    client.search({
        tableName: TABLE_NAME,
        indexName: INDEX_NAME,
        searchQuery: {
            offset: 0,
            limit: 10, // You can set limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
            query: { // Set the query type to TableStore.QueryType.GEO_BOUNDING_BOX_QUERY.
                queryType: TableStore.QueryType.GEO_BOUNDING_BOX_QUERY,
                query: {
                    fieldName: "Col_GeoPoint", // Set the column name.
                    topLeft: "10,0", // Specify the coordinate pair for the upper-left corner of the rectangular geographic area.
                    bottomRight: "0,10" // Specify coordinate pair for the lower-right corner of the rectangular geographic area.
                }
            },
            getTotalCount: true // The value of TotalCount indicates the number of rows that match the query conditions. By default, TotalCount is set to false, which indicates that the total number of matched rows is not returned. Set TotalCount to true in this example.
        },
        columnToGet: { // You can set RETURN_SPECIFIED to return the specified columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data from the table.
            returnType: TableStore.ColumnReturnType.RETURN_ALL
        }
    }, function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }
        console.log('success:', JSON.stringify(data, null, 2));
    });
    ```


## Geo-polygon query

You can use geo-polygon query to query data that falls within a geographic polygon area. You can specify the geographic polygon area as a query condition. Tablestore returns the rows where the value of a field falls within the geographic polygon area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |tableName|The name of the table.|
    |indexName|The name of the search index.|
    |query|The query statement for the search index. Sets the query type to TableStore.QueryType.GEO\_POLYGON\_QUERY.|
    |fieldName|The column name. Type: Geopoint.|
    |points|The coordinate pairs of the points that define a polygon area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of the longitude: \[-180,180\]. Example: "35.8,-45.91". |

-   Examples

    The following code provides an example on how to query the table for rows where the value of Col\_GeoPoint falls within a specified geographic polygon area:

    ```
    client.search({
        tableName: TABLE_NAME,
        indexName: INDEX_NAME,
        searchQuery: {
            offset: 0,
            limit: 10, // You can set limit to 0 if you do not need the specific data but the number of rows. The valve of 0 indicates that no data is returned.
            query: { // Set the query type to TableStore.QueryType.GEO_POLYGON_QUERY.
                queryType: TableStore.QueryType.GEO_POLYGON_QUERY,
                query: {
                    fieldName: "Col_GeoPoint",
                    points: ["0,0","5,5","5,0"] //  Specify coordinate pairs for vertices of a geographic polygon area.
                }
            },
            getTotalCount: true // The value of TotalCount indicates the number of rows that match the query conditions. By default, TotalCount is set to false, which indicates that the total number of matched rows is not returned. Set TotalCount to true in this example.
        },
        columnToGet: { // You can set RETURN_SPECIFIED to return the specified columns, RETURN_ALL to return all columns, or RETURN_NONE to return no data from the table.
            returnType: TableStore.ColumnReturnType.RETURN_ALL
        }
    }, function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }
        console.log('success:', JSON.stringify(data, null, 2));
    });
    ```


