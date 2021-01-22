# Features

This topic describes the core features of search index and the features that match SQL syntax.

## Core features

-   Queries based on non-primary key columns

    Tablestore supports queries only based on primary key columns or prefixes of primary key columns. Therefore, Tablestore cannot be applied to some query scenarios. Search index enables Tablestore to support queries based on non-primary key columns. You need only to create a search index for the column to query.

-   Boolean query

    Boolean query is suitable for order scenarios. In order scenarios, a table may contain dozens of fields. It may be difficult to determine how to combine the fields required for queries when you create a table. Even if you have determined how to combine the required fields, hundreds of combination methods may be available. If you use a relational database service, you have to create hundreds of indexes. In addition, if a combination method is not specified in advance, you cannot query the corresponding data.

    However, you can use Tablestore to create a search index that includes the required field names, which can be combined in a query. Search index also supports multiple logical operators such as AND, OR, and NOT.

-   Queries by geographical location

    As mobile devices gain popularity, geographical location data has become increasingly important. This data is used in most apps such as social media apps, food delivery apps, sports apps, and Internet of Vehicles \(IoV\) apps. These apps must support query features for the geographical location data that these apps provide.

    Search index supports queries based on the following geographical location data:

    -   Near: queries points within a specified radius based on a central point.
    -   Within: queries points within a specified rectangular or polygonal area.
    Tablestore allows you to use these features to query geographical location data without using other database services or search engines.

-   Full-text search

    Search index can tokenize data to perform full-text search. However, unlike search engines, Tablestore returns only BM25-based relevant results. Tablestore does not return custom relevant results in response to a query. If you need to query relevant results, we recommend that you use search engines.

    Search index supports the following five tokenization methods: single-word tokenization, delimiter tokenization, minimum semantic unit-based tokenization, maximum semantic unit-based tokenization, and fuzzy tokenization. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).

-   Fuzzy query

    Search index supports queries based on wildcards. This feature is similar to the LIKE operator in relational databases. You can specify characters and wildcards such as question marks \(`?`\) or asterisks \(`*`\) to query data in the way similar to the LIKE operator.

-   Prefix query

    Search index supports queries by prefix. This feature is applicable to any natural languages. For example, in a query based on prefix "apple", words such as "apple6s" and "applexr" may be returned.

-   Nested query

    In addition to a flat structure, online data such as labeled pictures has some complex multilayered structures. For example, assume that a database stores a large number of pictures, and each picture has multiple elements such as houses, cars, and people. Each element in a picture has a unique weight score. The score is evaluated based on the size and position of the element in a picture. Therefore, each picture has multiple tags. Each tag has a name and a weight score. You can use nested queries to query data based on the tags of the data.

    The following example provides an example on JSON data:

    ```
    {
     "tags": [
       {
          "name": "car",
          "score": 0.78
       },
       {
          "name": "tree",
          "score": 0.24
       }
     ]
    }
    ```

    You can use nested queries to store and query multilayered data. This feature makes it easy to model complex data.

-   Collapse

    Search index supports the collapse feature to deduplicate query results. The collapse feature allows you to specify the highest frequency of occurrence of an attribute value to achieve high cardinality. For example, when you search for a laptop on an e-commerce platform, the first page may display only products from a single brand, which is not user-friendly. You can use the collapse feature of Tablestore to resolve this issue.

-   Sorting

    In Tablestore, table data is sorted in alphabetical order of their primary key columns. To sort data based on other fields, you must use the sorting feature of search index. Tablestore supports a variety of sorting methods, including ascending, descending, single-field, and multi-field sorting. By default, returned results are sorted by the order of primary key values. You can use this method to sort data globally.

-   Total number of rows

    You can specify the number of rows that Tablestore returns for the current request when you use search index for a query. If you do not specify query conditions for search index, Tablestore returns the total number of rows for which you have created indexes. After you stop writing new data to a table and create indexes for all attributes, Tablestore returns the total number of rows in the table. This feature applies to data verification and data management.


## SQL

Tablestore does not support SQL statements or operators. However, most of SQL syntax can match similar features of search index, as described in the following table.

|SQL|Search Index|Supported|
|---|------------|---------|
|Show|API operation: DescribeSearchIndex|Supported|
|Select|Parameter: ColumnsToGet|Supported|
|From|Parameter: index name|Yes for single indexes and no for multiple indexes|
|Where|Query: a variety of queries such as term query|Supported|
|Order by|Parameter: sort|Supported|
|Limit|Parameter: limit|Supported|
|Delete|API operation: query followed by DeleteRow|Supported|
|Like|Query: wildcard query|Supported|
|And|Parameter: operator = and|Supported|
|Or|Parameter: operator = or|Supported|
|Not|Query: Boolean query|Supported|
|Between|Query: range query|Supported|
|Null|ExistQuery|Supported|

