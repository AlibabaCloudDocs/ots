# Timeline management

You can call the operations for fuzzy query and Boolean query to manage Timeline data.

The query operations work on the basis of the Search Index feature. Only the TimelineStore that has IndexSchema configured supports the query operations. An index can be of the LONG, DOUBLE, BOOLEAN, KEYWORD, GEO\_POINT, or TEXT type. The index attributes include Index, Store, Array, and Analyzer, and have the same descriptions as those of the Search Index feature. For more information, see [Overview](/intl.en-US/Function Introduction/Search Index/API operations/Overview.md).

## Search

You can call this operation to use Boolean query. This query requires the field for a fuzzy match. You need to set the index type of the field to TEXT, and specify the tokenizer.

```
/**
 * Search timeline by SearchParameter.
 * */
SearchParameter searchParameter = new SearchParameter(
        field("text").equals("fieldValue")
);
timelineStore.search(searchParameter);

/**
 * Search timeline by SearchQuery.
 * */
TermQuery query = new TermQuery();
query.setFieldName("text");
query.setTerm(ColumnValue.fromString("fieldValue"));
SearchQuery searchQuery = new SearchQuery().setQuery(query).setLimit(10);
timelineStore.search(searchQuery);
```

## Flush

The BatchStore operation works on the basis of the DefaultTableStoreWriter class in Tablestore SDKs. You can call the Flush operation to trigger the process of sending undelivered messages in the Buffer to Tablestore and wait until Tablestore stores all these messages.

```
/**
 * Flush messages in buffer, and wait until all messages are stored.
 * */
timelineStore.flush();
```

