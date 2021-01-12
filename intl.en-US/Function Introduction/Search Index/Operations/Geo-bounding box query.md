# Geo-bounding box query

You can use geo-bounding box query to query data that falls within a rectangular geographic area. You can specify the rectangular geographic area as a query condition. Tablestore returns the rows where the value of a field falls within the rectangular geographic area.

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column. Set the query type to Geopoint.|
|topLeft|The coordinate pair of the upper-left corner of the rectangular geographic area.|
|bottomRight|The coordinate pair of the lower-right corner of the rectangular geographic area. The coordinate pairs of the upper-left corner and lower-right corner define a unique rectangular geographic area.This parameter value must be in the format of `latitude,longitude`. Valid values of the latitude: \[-90,+90\]. Valid values of the longitude: \[-180,+180\]. Example: `35.8,-45.91`. |
|query|The query statement for the search index. Set the query type to GeoBoundingBoxQuery.|
|getTotalCount|Specifies whether to return the total number of rows that match the query conditions. By default, this parameter is set to false, which indicates that the total number of rows that match the query conditions is not returned. Query performance is affected when this parameter is set to true. |
|tableName|The name of the table.|
|indexName|The name of the search index.|
|columnsToGet|Specifies whether to return all columns of each matched row. You can configure returnAll and columns for this parameter. By default, returnAll is set to false, which indicates that not all columns are returned. If returnAll is set to false, you can use columns to specify the columns to return. If you do not specify the columns to return, only the primary key columns are returned.

If returnAll is set to true, all columns are returned. |

## Examples

The data type of Col\_GeoPoint is GeoPoint. You can obtain the rows where the value of Col\_GeoPoint falls within the rectangular geographic area where the upper-left corner is at "10,0" and the lower-right corner is at "0,10".

```
public static void geoBoundingBoxQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoBoundingBoxQuery geoBoundingBoxQuery = new GeoBoundingBoxQuery(); // Set the query type to GeoBoundingBoxQuery.
    geoBoundingBoxQuery.setFieldName("Col_GeoPoint"); // Set the name of the field that you want to match.
    geoBoundingBoxQuery.setTopLeft("10,0"); // Specify coordinates for the upper-left corner of the rectangular geographic area.
    geoBoundingBoxQuery.setBottomRight("0,10"); // Specify coordinates for the lower-right corner of the rectangular geographic area.
    searchQuery.setQuery(geoBoundingBoxQuery);
    //searchQuery.setGetTotalCount(true); // Specify that the total number of matched rows is returned.

    SearchRequest searchRequest = new SearchRequest("sampleTable", "sampleSearchIndex", searchQuery);
    // You can set the columnsToGet parameter to specify the columns to return or specify that all columns are returned. If you do not set this parameter, only the primary key columns are returned.
    //SearchRequest.ColumnsToGet columnsToGet = new SearchRequest.ColumnsToGet();
    //columnsToGet.setReturnAll(true); // Set ReturnAll to true to return all columns.
    //columnsToGet.setColumns(Arrays.asList("ColName1","ColName2")); // Set Columns to return specified columns.
    //searchRequest.setColumnsToGet(columnsToGet);

    SearchResponse resp = client.search(searchRequest);
    //System.out.println("TotalCount: " + resp.getTotalCount()); // Display the total number of matched rows instead of the number of returned rows.
    System.out.println("Row: " + resp.getRows());
}
```

