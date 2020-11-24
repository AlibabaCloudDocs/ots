# Geo-distance query

You can use geo-distance query to specify a circular geographical area that is defined by a central point and radius as a query condition. Tablestore returns the rows where the value of a field falls within the circular geographical area.

## Parameters

|Parameter|Description|
|---------|-----------|
|fieldName|The name of the column. Set the query type to Geopoint.|
|centerPoint|The coordinate pair of the central point. The coordinate pair consists of latitude and longitude values.This parameter value must be in the format of "latitude,longitude". Valid values of the latitude: \[-90,90\]. Valid values of longitude: \[-180,180\]. Example: “35.8,-45.91". |
|distanceInMeter|The radius of the circle centered on the specified location. Type: Double. Unit: meters.|
|query|The query statement for the search index. Set the query type to GeoDistanceQuery.|
|tableName|The name of the table.|
|indexName|The name of the search index.|

## Examples

Search the table for rows where the value of Col\_GeoPoint falls within a specified distance from a specified central point.

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

