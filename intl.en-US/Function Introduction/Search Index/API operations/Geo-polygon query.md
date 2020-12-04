# Geo-polygon query

You can use geo-polygon query to query data that falls within a polygon geographic area. You can specify the polygon geographic area as a query condition. Tablestore returns the rows where the value of a field falls within the polygon geographic area.

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column. Set the query type to Geopoint.|
|points|The coordinate pairs of the points that define a polygon area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of longitude: \[-180,180\]. Example: “35.8,-45.91". |
|query|The query statement for the search index. Set the query type to GeoPolygonQuery.|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when the total number of rows that match the query conditions is returned. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

Query the table for rows where the value of Col\_GeoPoint falls within a specified polygon geographic area.

```
public static void geoPolygonQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoPolygonQuery geoPolygonQuery = new GeoPolygonQuery();  // Set the query type to GeoPolygonQuery.
    geoPolygonQuery.setFieldName("Col_GeoPoint");
    geoPolygonQuery.setPoints(Arrays.asList("0,0","5,5","5,0")); // Specify coordinate pairs for vertices of a polygon geographic area.
    searchQuery.setQuery(geoPolygonQuery);
    //searchQuery.setGetTotalCount(true);//Set the total number of matched rows to return.

    SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    // You can set the columnsToGet parameter to specify the columns to return or specify to return all columns. If you do not set this parameter, only the primary key columns are returned.
    //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    //columnsToGet.setReturnAll(true); // Set returnAll to true to return all columns.
    //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set columns to return specified columns.
    //searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    //System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

