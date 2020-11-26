# Geo query

Geo query includes geo-distance query, geo-bounding box query, and geo-polygon query.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A data table is created. Data is written to the table.
-   A search index is created for the table. For more information, see [Create search indexes](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md).

## Geo-distance query

You can use geo-distance query to specify a circular geographical area consisting of a central point and radius as a filter condition in a query. Tablestore returns the rows where the values of a specified column fall within the circular geographical area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |index\_name|The name of the search index.|
    |query|The query statement for the search index. To use geo-distance query, set the query type to QueryTypeConst::GEO\_DISTANCE\_QUERY.|
    |field\_name|The name of the column used to match the query conditions. The specified column must be a GEOPOINT column.|
    |center\_point|The coordinate pair of the central point. The value of this parameter is of the GEOPOINT data type in the following format:`latitude,longitude`. Valid values of the latitude: \[-90,+90\]. Valid values of the longitude: \[-180,+180\]. Example: `35.8,-45.91`. |
    |distance|The radius of the circular geographical area. The value of this parameter is of the DOUBLE data type. Unit: meters.|

-   Examples

    The following code provides an example on how to query rows where the values of the geo column fall within the circular geographical area whose central point is `30.001,120.001` and radius is 1,000 meters:

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::GEO_DISTANCE_QUERY,
                'query' => array(
                    'field_name' => 'geo',
                    'center_point' => '30.001,120.001',
                    'distance' => 1000
                )
            ),
            'sort' => array(
                array(
                    'geo_distance_sort' => array(
                        'field_name' => 'geo',
                        'order' => SortOrderConst::SORT_ORDER_ASC,
                        'distance_type' => GeoDistanceTypeConst::GEO_DISTANCE_PLANE,
                        'points' => array('30,120')
                    )
                ),
            )
        ),
        'columns_to_get' => array(
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
            'return_names' => array('geo')
        )
    );
    $response = $otsClient->search($request);
    ```


## Geo-bounding box query

You can use geo-distance query to specify a rectangular geographical area. Tablestore returns the rows where the values of a specified column fall within the rectangular geographical area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |index\_name|The name of the search index.|
    |query|The query statement for the search index. To use geo-bounding query, set the query type to QueryTypeConst::GEO\_BOUNDING\_BOX\_QUERY.|
    |field\_name|The name of the column used to match the query conditions. The specified column must be a GEOPOINT column.|
    |top\_left|The coordinate pair of the upper-left corner of the rectangle box.|
    |bottom\_right|The coordinate pair of the lower-right corner of the rectangle box. A rectangular geographical area can be specified by a upper-left corner and a lower-right corner.The value of this parameter is in the following format: `latitude,longitude`. Valid values of the latitude: \[-90,+90\]. Valid values of the longitude: \[-180,+180\]. Example: `35.8,-45.91`. |

-   Examples

    The following code provides an example on how to query rows where the values of the geo column fall within the rectangular geographical area whose upper-left corner is at `31,119` and lower-right corner at `29,121`.

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::GEO_BOUNDING_BOX_QUERY,
                'query' => array(
                    'field_name' => 'geo',
                    'top_left' => '31,119',
                    'bottom_right' => '29,121'
                )
            ),
            'sort' => array(
                array(
                    'geo_distance_sort' => array(
                        'field_name' => 'geo',
                        'order' => SortOrderConst::SORT_ORDER_ASC,
                        'distance_type' => GeoDistanceTypeConst::GEO_DISTANCE_PLANE,
                        'points' => array('30,120')
                    )
                ),
            )
        ),
        'columns_to_get' => array(
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
            'return_names' => array('geo')
        )
    );
    $response = $otsClient->search($request);
    ```


## Geo-polygon query

You can use geo-polygon query to specify a polygonal geographical area. Tablestore returns the rows where the values of a specified column fall within the polygonal geographical area.

-   Parameters

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |index\_name|The name of the search index.|
    |query|The query statement for the search index. To use geo-polygon query, set the query type to QueryTypeConst::GEO\_POLYGON\_QUERY.|
    |field\_name|The name of the column used to match the query conditions. The specified column must be a GEOPOINT column.|
    |points|The coordinate pairs of points that uniquely define the polygon geographical area. You can specify a polygon by using multiple coordinates in the following format:`latitude,longitude`. Valid values of the latitude: \[-90,+90\]. Valid values of the longitude: \[-180,+180\]. Example: `35.8,-45.91`. |

-   Examples

    The following code provides an example on how to query rows where the values of the geo column fall within the polygonal geographical area defined by the coordinate pairs of `31,120`, `29,121`, and `29,119`:

    ```
    $request = array(
        'table_name' => 'php_sdk_test',
        'index_name' => 'php_sdk_test_search_index',
        'search_query' => array(
            'offset' => 0,
            'limit' => 2,
            'get_total_count' => true,
            'query' => array(
                'query_type' => QueryTypeConst::GEO_POLYGON_QUERY,
                'query' => array(
                    'field_name' => 'geo',
                    'points' => array(
                        "31,120",
                        "29,121",
                        "29,119"
                    )
                )
            ),
            'sort' => array(
                array(
                    'geo_distance_sort' => array(
                        'field_name' => 'geo',
                        'order' => SortOrderConst::SORT_ORDER_ASC,
                        'distance_type' => GeoDistanceTypeConst::GEO_DISTANCE_PLANE,
                        'points' => array('30,120')
                    )
                ),
            )
        ),
        'columns_to_get' => array(
            'return_type' => ColumnReturnTypeConst::RETURN_SPECIFIED,
            'return_names' => array('geo')
        )
    );
    $response = $otsClient->search($request);
    ```


