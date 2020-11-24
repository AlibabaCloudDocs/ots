# Geo query

Geo query includes the following query types: geo-distance query \(GeoDistanceQuery\), geo-bounding box query \(GeoBoundingBoxQuery\), and geo-polygon query \(GeoPolygonQuery\).

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created and data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/Java SDK/Search index/Create search indexes.md).

## Geo-distance query

You can use GeoDistanceQuery to specify a circular geographic area that is defined by a central point and a radius as a query condition. Tablestore returns the rows where the value of a field falls within the circular geographic area.

-   Parameter

    |Parameter|Description|
    |---------|-----------|
    |fieldName|The name of the column. Set the query type to Geopoint.|
    |centerPoint|The coordinate pair of the central point. The coordinate pair consists of latitude and longitude values.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of longitude: \[-180,180\]. Example: “35.8,-45.91". |
    |distanceInMeter|The radius of the circle centered on the specified location. Type: Double. Unit: meters.|
    |query|The query statement for the search index. Set the query type to GeoDistanceQuery.|
    |tableName|The name of the table.|
    |indexName|The name of the search index.|

-   Examples

    The following code provides an example on how to search the table for rows where the value of Col\_GeoPoint falls within a specified distance from a specified central point:

    ```
    /** 
     * @param client
     */
    public static void geoDistanceQuery(SyncClient client) {
        SearchQuery searchQuery = new SearchQuery();
        GeoDistanceQuery geoDistanceQuery = new GeoDistanceQuery();  // Set the query type to GeoDistanceQuery.
        geoDistanceQuery.setFieldName("Col_GeoPoint");
        geoDistanceQuery.setCenterPoint("5,5"); // Specify the coordinate pair for a central point.
        geoDistanceQuery.setDistanceInMeter(10000); // You can set the distance from the central point to a value greater than or equal to 10,000. Unit: meters.
        searchQuery.setQuery(geoDistanceQuery);
    
        SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    
        SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
        columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  // Specify that the Col_GeoPoint column is returned.
        searchRequest.setColumnsToGet(columnsToGet);
    
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
        System.out.println("Row: " + resp.getRows());
    }
    ```


## Geo-bounding box query

You can use geo-bounding box query to query data that falls within a rectangular geographic area. You can specify the rectangular geographic area as a query condition. Tablestore returns the rows where the value of a field falls within the rectangular geographic area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |fieldName|The name of the column. Set the query type to Geopoint.|
    |topLeft|The coordinate pair of the upper-left corner of the rectangular geographic area.|
    |bottomRight|The coordinate pair of the lower-right corner of the rectangular geographic area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of longitude: \[-180,180\]. Example: “35.8,-45.91". |
    |query|The query statement for the search index. Ste the query type to GeoBoundingBoxQuery.|
    |tableName|The name of the table.|
    |indexName|The name of the search index.|

-   Examples

    The data type of Col\_GeoPoint is GeoPoint. The coordinate pair of the upper-left corner is "10,0". The coordinate pair of the lower-right corner is "0,10". The following code provides an example on how to obtain the rows where the value of Col\_GeoPoint falls within the rectangular geographic area:

    ```
    /**
     * @param client
     */
    public static void geoBoundingBoxQuery(SyncClient client) {
        SearchQuery searchQuery = new SearchQuery();
        GeoBoundingBoxQuery geoBoundingBoxQuery = new GeoBoundingBoxQuery(); // Set the query type to GeoBoundingBoxQuery.
        geoBoundingBoxQuery.setFieldName("Col_GeoPoint"); // Set the name of the field that you want to match.
        geoBoundingBoxQuery.setTopLeft("10,0"); // Specify the coordinate pair for the upper-left corner of the rectangular geographic area.
        geoBoundingBoxQuery.setBottomRight("0,10"); // Specify coordinate pair for the lower-right corner of the rectangular geographic area.
        searchQuery.setQuery(geoBoundingBoxQuery);
    
        SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    
        SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
        columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  // Specify that the Col_GeoPoint column is returned.
        searchRequest.setColumnsToGet(columnsToGet);
    
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
        System.out.println("Row: " + resp.getRows());
    }
    ```


## Geo-polygon query

You can use geo-polygon query to query data that falls within a geographic polygon area. You can specify the geographic polygon area as a query condition. Tablestore returns the rows where the value of a field falls within the geographic polygon area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |fieldName|The name of the column. Set the query type to Geopoint.|
    |points|The coordinate pairs of the points that define a polygon area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of longitude: \[-180,180\]. Example: “35.8,-45.91". |
    |query|The query statement for the search index. Set the query type to GeoPolygonQuery.|
    |tableName|The name of the table.|
    |indexName|The name of the search index.|

-   Examples

    The following code provides an example on how to query the table for rows where the value of Col\_GeoPoint falls within a specified geographic polygon area:

    ```
    /**
     * Query the table for rows where the value of Col_GeoPoint falls within a specified polygon geographic area.
     * @param client
     */
    public static void geoPolygonQuery(SyncClient client) {
        SearchQuery searchQuery = new SearchQuery();
        GeoPolygonQuery geoPolygonQuery = new GeoPolygonQuery();  // Set the query type to GeoPolygonQuery.
        geoPolygonQuery.setFieldName("Col_GeoPoint");
        geoPolygonQuery.setPoints(Arrays.asList("0,0","5,5","5,0")); // Specify coordinate pairs for vertices of a polygon geographic area.
        searchQuery.setQuery(geoPolygonQuery);
    
        SearchRequest searchRequest = new SearchRequest(TABLE_NAME, INDEX_NAME, searchQuery);
    
        SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
        columnsToGet.setColumns(Arrays.asList("Col_GeoPoint"));  // Specify that the Col_GeoPoint column is returned.
        searchRequest.setColumnsToGet(columnsToGet);
    
        SearchResponse resp = client.search(searchRequest);
        System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
        System.out.println("Row: " + resp.getRows());
    }
    ```


