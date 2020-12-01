# How do I view the total count of rows in a table?

You can obtain the total count of rows in a table by calling the GetRange operation, using search index, or using Data Lake Analytics \(DLA\).

-   Call the [GetRange](/intl.en-US/API Reference/Operations/GetRange.md) operation

    Call the GetRange operation to calculate the count of rows in a table. High-concurrency levels and poor performance may result in latencies when you call this operation.

-   Use search index

    **Note:** To use search index, you must [Create search indexes](/intl.en-US/Function Introduction/Search Index/API operations/Create search indexes.md).

    -   Call MatchAllQuery of search index to return the total count of rows in a table within several milliseconds. For more information, see [Match all query](/intl.en-US/Function Introduction/Search Index/API operations/Match all query.md).
    -   Call aggregation of search index to return the total count of rows in a table within several milliseconds. For more information, see [Aggregation](/intl.en-US/Function Introduction/Search Index/API operations/Aggregation.md).

