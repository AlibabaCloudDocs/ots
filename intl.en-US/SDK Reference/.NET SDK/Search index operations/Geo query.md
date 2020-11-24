# Geo query

Geo query includes the following query types: geo-distance query \(GeoDistanceQuery\), geo-bounding box query \(GeoBoundingBoxQuery\), and geo-polygon query \(GeoPolygonQuery\).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/.NET SDK/Search index operations/Create search indexes.md).

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

    ```
    /// <summary> 
    /// Search the table for rows where the value of Col_GeoPoint falls within a specified distance from a specified central point.
    /// </summary>
    /// <param name="client"></param>
    public static void GeoDistanceQuery(OTSClient client)
    {
        SearchQuery searchQuery = new SearchQuery();
        GeoDistanceQuery geoDistanceQuery = new GeoDistanceQuery();  // Set the query type to GeoDistanceQuery.
        geoDistanceQuery.FieldName = Geo_type_col;
        geoDistanceQuery.CenterPoint = "10,11"; // Specify the coordinate pair for a central point.
        geoDistanceQuery.DistanceInMeter = 10000; // Set the distance from the central point to a value greater than or equal to 10,000. Unit: meters.
        searchQuery.Query = geoDistanceQuery;
    
        SearchRequest searchRequest = new SearchRequest(TableName, IndexName, searchQuery);
    
        ColumnsToGet columnsToGet = new ColumnsToGet();
        columnsToGet.Columns = new List<string>() { Geo_type_col };  // Specify that the Col_GeoPoint column is returned.
        searchRequest.ColumnsToGet = columnsToGet;
    
        SearchResponse response = client.Search(searchRequest);
        Console.WriteLine(response.TotalCount);
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

    ```
    /// <summary>
    /// The data type of geo_type_col is GeoPoint. The coordinate pair of the upper-left corner is "10,0". The coordinate pair of the lower-right corner is "0,10". You can obtain the rows where the value of geo_type_col falls within the rectangular geographic area.
    /// </summary>
    /// <param name="client"></param>
    public static void GeoBoundingBoxQuery(OTSClient client)
    {
        SearchQuery searchQuery = new SearchQuery();
        GeoBoundingBoxQuery geoBoundingBoxQuery = new GeoBoundingBoxQuery(); // Set the query type to GeoBoundingBoxQuery.
        geoBoundingBoxQuery.FieldName = Geo_type_col; // Set the column name.
        geoBoundingBoxQuery.setTopLeft("10,0"); // Specify the coordinate pair for the upper-left corner of the rectangular geographic area.
        geoBoundingBoxQuery.setBottomRight("0,10"); // Specify coordinate pair for the lower-right corner of the rectangular geographic area.
        searchQuery.Query = geoBoundingBoxQuery;
    
        SearchRequest searchRequest = new SearchRequest(TableName, IndexName, searchQuery);
    
        var columnsToGet = new ColumnsToGet();
        columnsToGet.Columns = new List<string> { Geo_type_col };  // Specify that the Col_GeoPoint column is returned.
        searchRequest.ColumnsToGet = columnsToGet;
    
        SearchResponse response = client.Search(searchRequest);
        Console.WriteLine(response.TotalCount);
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

    ```
    /// <summary>
    /// Query the table for rows where the value of geo_type_col falls within a specified geographic polygon area.
    /// </summary>
    /// <param name="client"></param>
    public static void GeoPolygonQuery(OTSClient client)
    {
        SearchQuery searchQuery = new SearchQuery();
        GeoPolygonQuery geoPolygonQuery = new GeoPolygonQuery();  // Set the query type to GeoPolygonQuery.
        geoPolygonQuery.FieldName = Geo_type_col;
        geoPolygonQuery.Points = new List<string>() { "0,0", "10,0", "10,10" }; // Specify coordinate pairs for vertices of a geographic polygon area.
        searchQuery.Query = geoPolygonQuery;
    
        SearchRequest searchRequest = new SearchRequest(TableName, IndexName, searchQuery);
    
        ColumnsToGet columnsToGet = new ColumnsToGet();
        columnsToGet.Columns = new List<string>() { Geo_type_col };  // Specify that the Col_GeoPoint column is returned.
        searchRequest.ColumnsToGet = columnsToGet;
    
        SearchResponse response = client.Search(searchRequest);
        Console.WriteLine(response.TotalCount);
    }
                
    ```


