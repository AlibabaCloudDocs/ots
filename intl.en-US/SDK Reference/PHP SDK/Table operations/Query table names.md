# Query table names

You can call the ListTable operation to query the names of all tables that are created in the current instance.

**Note:** For more information about the ListTable operation, see [ListTable](/intl.en-US/API Reference/Operations/ListTable.md).

## Prerequisites

-   OTSClient is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/PHP SDK/Initialization.md).
-   A table is created.

## Operations

```
/**
 * Query the names of all tables created in the current instance.
 * @api
 * @param [] $request The request parameters, which are empty.
 * @return [] Response. 
 * @throws OTSClientException The exception that is returned when a parameter error occurs or the Tablestore server returns a verification error.
 * @throws OTSServerException The exception that is returned when the Tablestore server returns an error.
 */
public function listTable(array $request); 
```

## Request format

The request parameters are empty. You do not need to set request parameters.

```
$result = $client->listTable([]);   
```

## Response format

A STRING list is returned. Each item in the list is a table name.

```
[
    '<string>',
    '<string>',
    '<string>'
]        
```

## Examples

The following code provides an example on how to query the names of all tables in an instance:

```
$result = $otsClient->listTable([]);         
```

