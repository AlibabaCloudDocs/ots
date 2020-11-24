# What do I do if no data can be found by calling the Search operation?

This topic describes the problem where no data is found by calling the Search operation, as well as possible causes of and solutions to the problem.

## Problem description

No data can be found when the Search operation is called.

## Causes

-   Data in the table is not properly synchronized to the search index.
    -   Data in the table is asynchronously synchronized to the search index. Latency in incremental data synchronization is typically less than 10 seconds, and that in full data synchronization is proportional to the volume of data in the table.
    -   Column names in the search index are case-sensitive, and may not match the column names in the table. For example, assume that a column in the table is named ColumnName. If the column in the search index is named columnname, it does not match the column in the table.
    -   The data type of a column in the search index does not match that in the table. For example, a column in the table is of the Integer data type, but the corresponding column in the search index is of the Keyword data type.

        **Note:** The Wide column model of Tablestore is a schema-free storage structure. The same attribute column can contain values of different data types, which may cause some rows to fail to be synchronized.

    -   The data type of a column in the search index does not match the data format in the table. For example, a column in the table is of the String data type and has a value of -91,100, whereas the corresponding column in the search index is of the Geopoint data type.

        **Note:** The geographical location data type in the search index is in the "latitude,longitude" format. The latitude must be in the range of −90 to +90, the longitude must be in the range of −180 to +180, and they must be specified in order.

-   The query method is incorrect.
    -   Match query is used to query data in fields of the Keyword data type in the search index. For example, assume that the query criterion MatchQuery\("ab"\) is set to query data "abc" in the search index while the corresponding column in the search index is of the Keyword data type. The query fails because the Keyword data type cannot be tokenized.
    -   The parameters specified in the query criterion are incorrectly configured. For example, assume that the query criterion TermQuery\("ab"\) is set to query data "abc" in the search index while the corresponding column in the search index is of the Keyword data type.
    -   Settings are incorrect when RangeQuery is used to query data in fields of the Keyword data type in the search index. For example, assume that the query criterion RangeQuery\(\>10\) is set to query data "abc" in the search index while the corresponding column in the search index is of the Keyword data type.

        **Note:** In RangeQuery, values of the Keyword data type are compared in alphabetical order, and values of the Long and Double data types are compared based on value sizes.

    -   The incorrect tokenizer is used. For example, the query criterion MatchQuery\("abcd"\) is set to query data "abcdefg" in the search index while the corresponding column in the search index is of the Text data type and the type of the tokenizer is set to the single-word tokenization.

## Solutions

-   When you create the search index, specify the column names and data types that are in correct correspondence with those in the table, and specify the data formats of data written to the table that match the data types in the search index. For more information, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/API operations/Overview.md) or [ARRAY and NEST data types](/intl.en-US/Function Introduction/Search Index/API operations/ARRAY and NEST data types.md).
-   Synchronize data in the table to the search index.
-   Select an appropriate query method and specify correct query criteria when you use search index. For more information, see [Query](/intl.en-US/Function Introduction/Search Index/API operations/Overview.md).
-   Use the correct tokenizer. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/API operations/Tokenization.md).

