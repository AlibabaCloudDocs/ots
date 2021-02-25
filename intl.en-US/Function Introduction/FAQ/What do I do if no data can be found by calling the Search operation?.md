# What do I do if no data can be found by calling the Search operation?

This topic describes the problem where no data is found by calling the Search operation, as well as possible causes of and solutions to the problem.

## Problem description

No data can be found when the Search operation is called.

## Cause

-   Data in the table is not properly synchronized to the search index.
    -   Data in the table is asynchronously synchronized to the search index. Latencies in incremental data synchronization are typically within 10 seconds, and that in full data synchronization is directly proportional to the volume of data in the table.
    -   Column names in the search index are case-sensitive, and may not match the column names in the table. For example, assume that a column in the table is named ColumnName. If the column in the search index is named columnname, columnname does not match the column in the table.
    -   The data type of a column in the search index does not match that in the table. For example, a column in the table is of the INTEGER data type, but the corresponding column in the search index is of the KEYWORD data type.

        **Note:** The Wide column model of Tablestore is a schema-free storage structure. The same attribute column can contain values of different data types, which may cause some rows to fail to be synchronized.

    -   The data type of a column in the search index does not match the data format in the table. For example, a column in the table is of the STRING data type and has a value of -91,100, whereas the corresponding column in the search index is of the GEOPOINT data type.

        **Note:** The geographical location data type in the search index is in the "latitude,longitude" format. The latitude must be in the range of -90 to +90, the longitude must be in the range of -180 to +180, and the latitude and longitude must be specified in order.

-   The query method is incorrect.
    -   Match query is used to query data in fields of the KEYWORD data type in the search index. For example, assume that the query condition MatchQuery\("ab"\) is set to query data "abc" in the search index while the corresponding column in the search index is of the KEYWORD data type. The query fails because the KEYWORD data type cannot be tokenized.
    -   The parameters specified in the query condition are incorrectly configured. For example, assume that the query condition TermQuery\("ab"\) is set to query data "abc" in the search index while the corresponding column in the search index is of the KEYWORD data type.
    -   Settings are incorrect when RangeQuery is used to query data in fields of the KEYWORD data type in the search index. For example, assume that data in a table is 20, the query condition RangeQuery\(\>10\) is set to query data whose value is greater than 10 in the search index, and the corresponding column in the search index is of the KEYWORD data type.

        **Note:** In RangeQuery, values of the KEYWORD data type are compared in alphabetical order, and values of the LONG and DOUBLE data types are compared based on value sizes.

    -   The incorrect tokenizer is used. For example, the query condition MatchQuery\("abcd"\) is set to query data "abcdefg" in the search index while the corresponding column in the search index is of the TEXT data type and the type of the tokenizer is set to the single-word tokenization.

## Solution

-   When you create the search index, specify the column names and data types that are in correct correspondence with those in the table, and specify the data formats of data written to the table that match the data types in the search index. For more information, see [Data types of column values](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md) or [ARRAY and nested data types](/intl.en-US/Function Introduction/Search Index/Operations/ARRAY and nested data types.md).
-   Synchronize data from the table to the search index.
-   Select an appropriate query method and specify correct query conditions when you use search index. For more information, see [Query](/intl.en-US/Function Introduction/Search Index/Operations/Overview.md).
-   Make sure that the correct tokenizer is used. For more information, see [Tokenization](/intl.en-US/Function Introduction/Search Index/Operations/Tokenization.md).

