# Meta management

You can call operations such as Insert, Delete, Update, Read, and Search to manage Meta data.

The Search operation works on the basis of the Search Index feature. Only the MetaStore that has IndexSchema configured supports the Search operation. An index can be of the LONG, DOUBLE, BOOLEAN, KEYWORD, or GEO\_POINT type. The index attributes include Index, Store, and Array, and have the same descriptions as those of the Search Index feature. For more information, see [Overview](/intl.en-US/Function Introduction/Search Index/API operations/Overview.md).

## Insert

The TimelineIdentifier value is used to uniquely identify Timeline data. Tablestore overwrites repeated TimelineIdentifier values.

```
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();
TimelineMeta meta = new TimelineMeta(identifier)
        .setField("filedName", "fieldValue");

timelineMetaStore.insert(meta);
```

## Read

You can call this operation to read TimelineMeta data in one row based on the TimelineIdentifier value.

```
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();

timelineMetaStore.read(identifier);
```

## Update

You can call this operation to update the Meta attribute that corresponds to the specified TimelineIdentifier value.

```
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();
TimelineMeta meta = new TimelineMeta(identifier)
        .setField("filedName", "new value");

timelineMetaStore.update(meta);
```

## Delete

You can call this operation to delete the TimelineMeta data in one row based on the TimelineIdentifier value.

```
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();

timelineMetaStore.delete(identifier);
```

## Search

You can call this operation to specify two search parameters: SearchParameter and SearchQuery. This operation returns Iterator<TimelineMeta\>. You can iterate all result sets by using the iterator.

```
/**
 * Search meta by SearchParameter.
 * */
SearchParameter parameter = new SearchParameter(
        field("fieldName").equals("fieldValue")
);
timelineMetaStore.search(parameter);

/**
 * Search meta by SearchQuery.
 * */
TermQuery query = new TermQuery();
query.setFieldName("fieldName");
query.setTerm(ColumnValue.fromString("fieldValue"));

SearchQuery searchQuery = new SearchQuery().setQuery(query);
timelineMetaStore.search(searchQuery);
```

