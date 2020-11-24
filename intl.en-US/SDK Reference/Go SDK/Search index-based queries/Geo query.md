# Geo query

Geo query includes the following query types: geo-distance query \(GeoDistanceQuery\), geo-bounding box query \(GeoBoundingBoxQuery\), and geo-polygon query \(GeoPolygonQuery\).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Go SDK/Search index-based queries/Create search indexes.md).

## Geo-distance query

You can use GeoDistanceQuery to specify a circular geographical area that is defined by a central point and a radius as a query condition. Tablestore returns the rows where the value of a field falls within the circular geographical area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |FieldName|The column name. Type: Geopoint.|
    |CenterPoint|The coordinate pair of the central point. The coordinate pair consists of latitude and longitude values.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of the longitude: \[-180,180\]. Example: "35.8,-45.91". |
    |DistanceInMeter|The radius of the circle centered on the specified location. Type: Double. Unit: meters.|
    |Query|The query statement for the search index. Sets the query type to GeoDistanceQuery.|
    |TableName|The name of the table.|
    |IndexName|The name of the search index.|

-   Examples

    The following code provides an example on how to search the table for rows where the value of Col\_GeoPoint falls within a specified distance from a specified central point:

    ```
    func GeoDistanceQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
        searchRequest.SetTableName(tableName)
        searchRequest.SetIndexName(indexName)
        query := &search.GeoDistanceQuery{} // Set the query type to GeoDistanceQuery.
        query.FieldName = "Col_GeoPoint"
        query.CenterPoint = "5,5"  // Specify the coordinate pair for a central point.
        query.DistanceInMeter = 10000.0 // Set the distance from the central point to a value greater than or equal to 10,000. Unit: meters.
        searchQuery := search.NewSearchQuery()
        searchQuery.SetQuery(query)
        searchRequest.SetSearchQuery(searchQuery)
        // Return all columns.
        searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
            ReturnAll:true,
        })
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
        fmt.Println("TotalCount: ", searchResponse.TotalCount) // Display the total number of matched rows.
        fmt.Println("RowCount: ", len(searchResponse.Rows))
        for _, row := range searchResponse.Rows {
            jsonBody, err := json.Marshal(row)
            if err != nil {
                panic(err)
            }
            fmt.Println("Row: ", string(jsonBody))
        }
    }
    ```


## Geo-bounding box query

You can use geo-bounding box query to query data that falls within a rectangular geographic area. You can specify the rectangular geographic area as a query condition. Tablestore returns the rows where the value of a field falls within the rectangular geographic area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |FieldName|The column name. Type: Geopoint.|
    |TopLeft|The coordinate pair of the upper-left corner of the rectangular geographic area.|
    |BottomRight|The coordinate pair of the lower-right corner of the rectangular geographic area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of the longitude: \[-180,180\]. Example: "35.8,-45.91". |
    |Query|The query statement for the search index. Sets the query type to GeoBoundingBoxQuery.|
    |TableName|The name of the table.|
    |IndexName|The name of the search index.|

-   Examples

    The data type of Col\_GeoPoint is GeoPoint. The coordinate pair of the upper-left corner is "10,0". The coordinate pair of the lower-right corner is "0,10". The following code provides an example on how to obtain the rows where the value of Col\_GeoPoint falls within the rectangular geographic area:

    ```
    func GeoBoundingBoxQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
        searchRequest.SetTableName(tableName)
        searchRequest.SetIndexName(indexName)
        query := &search.GeoBoundingBoxQuery{} // Set the query type to GeoBoundingBoxQuery.
        query.FieldName = "Col_GeoPoint" // Set the column name.
        query.TopLeft = "10,0"  // Specify the coordinate pair for the upper-left corner of the rectangular geographic area.
        query.BottomRight = "0,10" // Specify coordinate pair for the lower-right corner of the rectangular geographic area.
        searchQuery := search.NewSearchQuery()
        searchQuery.SetQuery(query)
        searchRequest.SetSearchQuery(searchQuery)
        // Return all columns.
        searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
            ReturnAll:true,
        })
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
        fmt.Println("TotalCount: ", searchResponse.TotalCount) // Display the total number of matched rows.
        fmt.Println("RowCount: ", len(searchResponse.Rows))
        for _, row := range searchResponse.Rows {
            jsonBody, err := json.Marshal(row)
            if err != nil {
                panic(err)
            }
            fmt.Println("Row: ", string(jsonBody))
        }
    }
    ```


## Geo-polygon query

You can use geo-polygon query to query data that falls within a geographic polygon area. You can specify the geographic polygon area as a query condition. Tablestore returns the rows where the value of a field falls within the geographic polygon area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |FieldName|The column name. Type: Geopoint.|
    |Points|The coordinate pairs of the points that define a polygon area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of the longitude: \[-180,180\]. Example: "35.8,-45.91". |
    |Query|The query statement for the search index. Sets the query type to GeoPolygonQuery.|
    |TableName|The name of the table.|
    |IndexName|The name of the search index.|

-   Examples

    The following code provides an example on how to query the table for rows where the value of Col\_GeoPoint falls within a specified geographic polygon area:

    ```
    func GeoPolygonQuery(client *tablestore.TableStoreClient, tableName string, indexName string) {
        searchRequest := &tablestore.SearchRequest{}
        searchRequest.SetTableName(tableName)
        searchRequest.SetIndexName(indexName)
        query := &search.GeoPolygonQuery{} // Set the query type to GeoDistanceQuery.
        query.FieldName = "Col_GeoPoint"
        query.Points = []string{"0,0","5,5","5,0"} // Specify coordinate pairs for vertices of a geographic polygon area.
        searchQuery := search.NewSearchQuery()
        searchQuery.SetQuery(query)
        searchRequest.SetSearchQuery(searchQuery)
        // Return all columns.
        searchRequest.SetColumnsToGet(&tablestore.ColumnsToGet{
            ReturnAll:true,
        })
        searchResponse, err := client.Search(searchRequest)
        if err != nil {
            fmt.Printf("%#v", err)
            return
        }
        fmt.Println("IsAllSuccess: ", searchResponse.IsAllSuccess) // Check whether all data is returned.
        fmt.Println("TotalCount: ", searchResponse.TotalCount) // Display the total number of matched rows.
        fmt.Println("RowCount: ", len(searchResponse.Rows))
        for _, row := range searchResponse.Rows {
            jsonBody, err := json.Marshal(row)
            if err != nil {
                panic(err)
            }
            fmt.Println("Row: ", string(jsonBody))
        }
    }
    ```


