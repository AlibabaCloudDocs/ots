# Overview

This topic describes the data types of column values, API operations, parameters used in the search operation, query features, usage, and billing of search indexes.

## Data types of column values

The value of a column in a search index is the value of the column of the same name from the corresponding table. The data types of the two values must match. The following table describes the matching rules.

|Data type of the column value in the search index|Data type of the column value in the table|Description|
|-------------------------------------------------|------------------------------------------|-----------|
|Long|Integer|64-bit long integers.|
|Double|Double|64-bit double-precision floating-point numbers.|
|Boolean|Boolean|Boolean values.|
|Keyword|String|Character strings that cannot be tokenized.|
|Text|String|Character strings or text that can be tokenized. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md). |
|Geopoint|String|Geographical coordinates in the `latitude,longitude` format. Valid values of the latitude: \[-90,+90\]. Valid values of the longitude: \[-180,+180\]. Example: `35.8,-45.91`.|
|[Nested](/intl.en-US/Function Introduction/Search Index/Operations/ARRAY and Nested field types.md)|String|Nested type values. Example: \{\["a": 1\], \["a": 3\]\}.|

**Note:** The data types of the values in the search index and the table must match each other based on the rules described in the preceding table. Otherwise, Tablestore discards the data as dirty data. Ensure that column values of the GEOPOINT and NESTED types comply with the formats described in the preceding table. If the formats do not match, Tablestore discards the data as dirty data. In this case, the data may be available in the table but unavailable in the search index.

The following table describes the attributes of columns in search indexes.

|Attribute|Type|Description|
|---------|----|-----------|
|Index|Boolean|Specifies whether to index a column. -   True indicates that Tablestore creates an inverted index or spatial index for the column.
-   False indicates that Tablestore does not create an index for the column.

If the column is not indexed, you cannot query data based on the column. |
|EnableSortAndAgg|Boolean|Specifies whether to enable the sorting and aggregation features. -   True indicates that data can be sorted by using the column, and values of the column can be aggregated.
-   False indicates that data cannot be sorted by using the column, and values of the column cannot be aggregated. |
|Store|Boolean|Specifies whether to store the value of the column in the search index. True indicates that Tablestore stores the values of the column in the search index. You can query the search index for the value of the column without querying the table. This improves the query performance. |
|IsArray|Boolean|Specifies whether the value of the column is an array. True indicates that the value of the column is an array. Data written to the column must be a JSON array, such as \["a","b","c"\].

You do not need to specify the Array parameter for a NESTED column because a NESTED column is an array.

Array type data can be used in all queries because arrays do not affect queries. |

The following table lists the combinations of column types and column attributes.

|Type|Index|EnableSortAndAgg|Store|IsArray|
|----|-----|----------------|-----|-------|
|Long|Supported|Supported|Supported|Supported|
|Double|Supported|Supported|Supported|Supported|
|Boolean|Supported|Supported|Supported|Supported|
|keyword|Supported|Supported|Supported|Supported|
|Text|Supported|Not supported|Supported|Supported|
|Geopoint|Supported|Supported|Supported|Supported|
|[Nested](/intl.en-US/Function Introduction/Search Index/Operations/ARRAY and Nested field types.md)|Applies only to child fields.|Applies only to child fields.|Applies only to child fields.|Nested fields are arrays.|

## API operations

|API operation|Description|
|-------------|-----------|
|[CreateSearchIndex](/intl.en-US/Function Introduction/Search Index/Operations/Create search indexes.md)|Creates a search index.|
|[DescribeSearchIndex](/intl.en-US/Function Introduction/Search Index/Operations/Query the description of a search index.md)|Queries detailed information of a search index.|
|[ListSearchIndex](/intl.en-US/Function Introduction/Search Index/Operations/List search indexes.md)|Queries the list of search indexes.|
|[DeleteSearchIndex](/intl.en-US/Function Introduction/Search Index/Operations/Delete search indexes.md)|Deletes a specified search index.|
|[Search](#section_26g_8d1_4b9)|You can call this operation to implement all query features and analysis features such as sorting and aggregation. The results are returned in a specified order.|
|[ParallelScan](/intl.en-US/Function Introduction/Search Index/Operations/Parallel scan.md)|You can call this operation to implement all query features, but you cannot call this operation to sort or aggregate data. Data that matches the query conditions is returned more quickly.|

## Query

You must specify SearchRequest in a query. The following table describes the parameters that are included in SearchRequest.

|Parameter|Type|Description|
|---------|----|-----------|
|offset|Integer|Specifies the position from which the current query starts.|
|limit|Integer|Specifies the maximum number of items that the current query returns.|
|getTotalCount|Boolean|Specifies whether to return the total number of matched rows. By default, this parameter is set to false. A value of true may affect the query performance.|
|[Sort](/intl.en-US/Function Introduction/Search Index/Operations/Sorting and pagination.md)|Sort|Specifies the column and method for sorting.|
|[Collapse](/intl.en-US/Function Introduction/Search Index/Operations/Collapse (distinct).md)|Collapse|Specifies the name of the column that you want to collapse in the query result.|
|query|Query|Specifies the type of the current query. The following table lists the query types.|

|Name|Query|Description|
|----|-----|-----------|
|Match all query|[MatchAllQuery](/intl.en-US/Function Introduction/Search Index/Operations/Match all query.md)|This query is used to query the total number of rows in a table or retrieve several rows from a table.|
|Match query|[MatchQuery](/intl.en-US/Function Introduction/Search Index/Operations/Match query.md)|This query uses approximate matches to retrieve query results. The keyword you use for a query is tokenized and then be used to match the query results. The relationship between different tokenized keywords is Or. When the tokenized keywords are partially matched, the row data meets the query condition. |
|Phrase query|[MatchPhraseQuery](/intl.en-US/Function Introduction/Search Index/Operations/Match phrase query.md)|This query is similar to match query, but the row data meets the query conditions only when the tokenized words match the row data in the same sequence and position.|
|Term query|[TermQuery](/intl.en-US/Function Introduction/Search Index/Operations/Term query.md)|This query uses full and exact matches to retrieve query results, which is similar to string matching. If a Text column is queried, the row data meets the query conditions when one of the tokenized words can be exactly matched. |
|Terms query|[TermsQuery](/intl.en-US/Function Introduction/Search Index/Operations/Terms query.md)|This query is similar to term query, but you can specify multiple words at a time. If one of the words is matched, the row data meets the query conditions.|
|Prefix query|[PrefixQuery](/intl.en-US/Function Introduction/Search Index/Operations/Prefix query.md)|This query retrieves data that contains the specified prefix. If a Text column is queried, the row data meets the query conditions when one of the tokenized words contains the specified prefix. |
|Range query|[RangeQuery](/intl.en-US/Function Introduction/Search Index/Operations/Range query.md)|This query retrieves data within a specified range. If a Text column is queried, the row data meets the query conditions when one of the tokenized words is within the specified range. |
|Wildcard query|[WildcardQuery](/intl.en-US/Function Introduction/Search Index/Operations/Wildcard query.md)|This query retrieves data that matches a string that contains one or more wildcard characters. You can use asterisks \(\*\) and question marks \(?\) as wildcards. An asterisk \(\*\) indicates any number of characters. A question mark \(?\) indicates any single character. |
|Boolean query|[BoolQuery](/intl.en-US/Function Introduction/Search Index/Operations/Boolean query.md)|You can use BoolQuery to query data based on a combination of filter conditions. This query contains one or more subqueries as filter conditions. Table Store returns the rows that match the subqueries. Subquery conditions can be combined by using logical operators such as AND, NOT, and OR. |
|Nested query|[NestedQuery](/intl.en-US/Function Introduction/Search Index/Operations/Nested query.md)|You can use a nested query to query the data of nested fields.|
|Geo-distance query|[GeoDistanceQuery](/intl.en-US/Function Introduction/Search Index/Operations/Geo-distance query.md)|You can specify a circular geographical area as a filter condition in a query, which consists of a central point and radius. Tablestore returns the rows where the value of a column falls within the geographical circular area.|
|Geo-bounding box query|[GeoBoundingBoxQuery](/intl.en-US/Function Introduction/Search Index/Operations/Geo-bounding box query.md)|You can specify a rectangular geographical area as a filter condition in a query. Tablestore returns the rows where the value of a column falls within the rectangular geographical area.|
|Geo-polygon query|[GeoPolygonQuery](/intl.en-US/Function Introduction/Search Index/Operations/Geo-polygon query.md)|You can specify a polygonal geographical area as a filter condition in a query. Tablestore returns the rows where the value of a field falls within the polygonal geographical area.|
|Exists query|[ExistQuery](/intl.en-US/Function Introduction/Search Index/Operations/Exists query.md)|This query is also called a NULL query or NULL-value query. This query is used for sparse data to determine whether a column in a row exists. For example, you can query the rows in which the value of the address column is not empty.If a column does not exist in the row or the value of the column is an empty array \("\[\]"\), you can determine that the column does not exist. |

## Usage

You can use the following Tablestore SDKs to implement search index:

-   [Java SDK](/intl.en-US/SDK Reference/Java SDK/Search Index/Create search indexes.md)
-   [Go SDK](/intl.en-US/SDK Reference/Go SDK/Search Index/Create search indexes.md)
-   [Python SDK](/intl.en-US/SDK Reference/Python SDK/Search Index/Create search indexes.md)
-   [Node.js SDK](/intl.en-US/SDK Reference/Node.js SDK/Search Index/Create search indexes.md)
-   [.NET SDK](/intl.en-US/SDK Reference/.NET SDK/Search Index/Create search indexes.md)
-   [PHP SDK](/intl.en-US/SDK Reference/PHP SDK/Search index/Create search indexes.md)

## Billing

For more information, see [Billable items of search indexes](/intl.en-US/Pricing/Billable items of search indexes.md).

