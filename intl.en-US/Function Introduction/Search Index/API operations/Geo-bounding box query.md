# Geo-bounding box query

You can use geo-bounding box query to query data that falls within a rectangular geographic area. You can specify the rectangular geographic area as a query condition. Tablestore returns the rows where the value of a field falls within the rectangular geographic area.

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column. Set the query type to Geopoint.|
|topLeft|The coordinate pair of the upper-left corner of the rectangular geographic area.|
|bottomRight|The coordinate pair of the lower-right corner of the rectangular geographic area. The coordinate pairs of the upper-left corner and lower-right corner define a unique rectangular geographic area.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90, 90\]. Valid values of the longitude: \[-180, 180\]. Example: “35.8,-45.91". |
|query|The query statement for the search index. Set the query type to GeoBoundingBoxQuery.|
|tableName|The name of the table.|
|indexName|The name of the search index.|

## Examples

\* The data type of Col\_GeoPoint is GeoPoint. You can obtain the rows where the value of Col\_GeoPoint falls within the rectangular geographic area where the upper-left corner is at "10,0" and the lower-right corner is at "0,10".

```
/**
 * @param client
 */public static void geoBoundingBoxQuery(SyncClient client) {
    SearchQuery searchQuery = new SearchQuery();
    GeoBoundingBoxQuery geoBoundingBoxQuery = new GeoBoundingBoxQuery(); // Set the query type to GeoBoundingBoxQuery.
    geoBoundingBoxQuery.setFieldName("Col_GeoPoint"); // Set the name of the field that you want to match.
    geoBoundingBoxQuery.setTopLeft("10,0"); // Specify coordinates for the upper-left corner of the rectangular geographic area.
    geoBoundingBoxQuery.setBottomRight("0,10"); // Specify coordinates for the lower-right corner of the rectangular geographic area.
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

