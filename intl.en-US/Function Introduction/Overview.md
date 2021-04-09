# Overview

Tablestore is a multi-model service that is developed by Alibaba Cloud. Tablestore can store a large amount of structured data and supports fast query and analysis. The distributed storage and powerful index-based search engine allow Tablestore to store petabytes of data while transactions per second \(TPS\) of tens of millions and a latency within milliseconds are ensured.

## Terms

Before you use Tablestore, you must understand the terms described in the following table.

|Term|Description|
|----|-----------|
|[Instance](/intl.en-US/Function Introduction/Terms/Instance.md)|An entity that uses and manages Tablestore. Tablestore implements access control and resource metering for applications at the instance level.|
|[Read/write throughput](/intl.en-US/Function Introduction/Terms/Read/write throughput.md)|The read/write throughput is measured by read/write capacity units \(CUs\), which is the smallest billing unit for read and write operations.|
|[Region](/intl.en-US/Function Introduction/Terms/Region.md)|A region is a physical data center of Alibaba Cloud.|
|[Endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md)|Each Tablestore instance has an endpoint. An endpoint must be specified before operations can be performed on tables or data in Tablestore.|

## Models

Tablestore provides multiple models that you can select and apply to your business. The following table describes the models of Tablestore.

|Model|Description|
|-----|-----------|
|[Wide Column model](/intl.en-US/Function Introduction/Wide Column model/Overview.md)|The Wide Column model is applicable to various scenarios such as metadata and big data. This model supports multiple features such as data versions, time to live \(TTL\), auto-increment of primary key columns, conditional updates, atomic counters, and filters.|
|[Timeline model](/intl.en-US/Function Introduction/Timeline model/Overview.md)|The Timeline model is a data model that is suitable for scenarios such as instant messaging \(IM\) and feed streams. This model can also meet requirements of message data scenarios, such as message order preservation, storage of large numbers of messages, and real-time synchronization. This model also supports full-text search and Boolean query.|

## Features

The following table describes the features provided by Tablestore.

|Feature|Description|
|-------|-----------|
|[Auto-increment of primary key columns](/intl.en-US/Function Introduction/Wide Column model/Auto-increment of primary key columns.md)|After you set a primary key column \(non-partition key\) to an auto-increment column, you do not need to enter values in this column when you write data in a row. Instead, Tablestore automatically generates primary key column values. The automatically generated values are unique within the rows that share the same partition key. These values increase sequentially.|
|[Conditional update](/intl.en-US/Function Introduction/Wide Column model/Conditional update.md)|If you use conditional update, data in the table can be updated only when the conditions are met. If the conditions are not met, the update fails.|
|[Atomic counters](/intl.en-US/Function Introduction/Wide Column model/Atomic counters.md)|Columns are used as an atomic counter. The atomic counter provides real-time statistics for some online applications, such as calculating the real-time page views \(PVs\) of a post.|
|[Filter](/intl.en-US/Function Introduction/Wide Column model/Filter.md)|Filters can be used to sort results on the server side. Only results that match the filter conditions are returned. The feature effectively reduces the volume of transferred data and shortens the response time.|
|[Search index](/intl.en-US/Function Introduction/Search Index/Overview.md)|Based on inverted index and column-oriented storage, search index solves the complex query problem in big data scenarios.|
|[Secondary index](/intl.en-US/Function Introduction/Global secondary index/Usage notes.md)|Tablestore allows you to create a secondary index on an attribute column.|
|[Tunnel Service](/intl.en-US/Function Introduction/Tunnel service/Overview.md)|Tunnel Service provides tunnels that are used to export and consume distributed data in full, incremental, and differential modes in real time. After tunnels are created, you can consume historical and incremental data exported from a specified table.|
|[HBase support](/intl.en-US/Function Introduction/HBase/Features of Tablestore HBase Client.md)|Tablestore HBase Client can be used to access Tablestore by using Java applications built on open source HBase APIs.|

