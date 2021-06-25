# Scenarios

The secondary index feature allows you to create an index on the specified columns. Data in the generated index table is sorted by the specified indexed columns. All data written to the data table is automatically synchronized to the index table. If you only write data to the data table and then query the index table that is created on the data table, the query performance can be improved in most scenarios.

## Global secondary index

You can use the global secondary index feature to perform various queries based on a data table of phone calls.

The following data table records the information about phone calls. When a phone call is complete, the information about the phone call is recorded in the data table.

-   The CellNumber and StartTime columns act as the primary key columns of the data table. CellNumber indicates the caller, and StartTime the call start time.
-   CalledNumber, Duration, and BaseStationNumber act as the predefined columns of the data table. CalledNumber indicates the number of a call recipient, Duration the duration of the call, and BaseStationNumber the base station number.

|CellNumber|StartTime \(UNIX timestamp\)|CalledNumber|Duration|BaseStationNumber|
|:---------|:---------------------------|:-----------|:-------|:----------------|
|123456|1532574644|654321|60|1|
|234567|1532574714|765432|10|1|
|234567|1532574734|123456|20|3|
|345678|1532574795|123456|5|2|
|345678|1532574861|123456|100|2|
|456789|1532584054|345678|200|3|

You can create an index table based on the CalledNumber and BaseStationNumber columns to perform various queries. For the sample code that is used to create an index table, see [Appendix](/intl.en-US/Function Introduction/Global secondary index/Appendix.md).

You can use the global secondary index feature to meet the following query requirements:

-   Query the rows where the value of CellNumber is 234567.

    Tablestore sorts rows in a data table based on their primary keys and provides the getRange operation. You can call the getRange operation to query the rows where the value of CellNumber is 234567. Set both the maximum and minimum values of CellNumber to 234567, and the minimum value of StartTime to 0 and the maximum value to INT\_MAX. Then, scan the data table to obtain the expected data.

    ```
    private static void getRangeFromMainTable(SyncClient client, long cellNumber){
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
    
        // Construct the start primary key. 
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(0));
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Construct the end primary key. 
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strNum = String.format("%d", cellNumber);
        System.out.println("The cell number " + strNum + "makes the following calls:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue to read data. 
            if (getRangeResponse.getNextStartPrimaryKey() != null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

-   Query the rows where the value of CalledNumber is 123456.

    Tablestore sorts rows in a data table based on their primary keys. Queries that involve predefined columns such as the CalledNumber column in this table are slow and inefficient. You can create an index table named IndexOnBeCalledNumber based on the CalledNumber column. CalledNumber becomes the primary key column of the index table. Therefore, you can call the getRange operation to scan the index table to obtain the expected data.

    The following table provides the information of IndexOnBeCalledNumber.

    **Note:** Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. The primary key columns of the data table are added to the index table and act as the primary key columns in the index table. Therefore, the index table contains three primary key columns.

    |PK0|PK1|PK2|
    |:--|:--|:--|
    |CalledNumber|CellNumber|StartTime|
    |123456|234567|1532574734|
    |123456|345678|1532574795|
    |123456|345678|1532574861|
    |654321|123456|1532574644|
    |765432|234567|1532574714|
    |345678|456789|1532584054|

    ```
    private static void getRangeFromIndexTable(SyncClient client, long cellNumber) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX0_NAME);
    
        // Construct the start primary key. 
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Construct the end primary key. 
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strNum = String.format("%d", cellNumber);
        System.out.println("The cell number" + strNum + "is called by the following numbers");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue to read data. 
            if (getRangeResponse.getNextStartPrimaryKey() != null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

-   Query the rows where the value of BaseStationNumber is 002 and the value of StartTime is 1532574740.

    This query is similar to the query in the preceding example but uses two query conditions: BaseStationNumber and StartTime. You can create an index table named IndexOnBaseStation1 based on BaseStationNumber and StartTime. Then, you can query IndexOnBaseStation1 to obtain the expected data.

    The following table provides the information of IndexOnBaseStation1.

    |PK0|PK1|PK2|
    |:--|:--|:--|
    |BaseStationNumber|StartTime|CellNumber|
    |001|1532574644|123456|
    |001|1532574714|234567|
    |002|1532574795|345678|
    |002|1532574861|345678|
    |003|1532574734|234567|
    |003|1532584054|456789|

    ```
    private static void getRangeFromIndexTable(SyncClient client,
                                               long baseStationNumber,
                                               long startTime) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX1_NAME);
    
        // Construct the start primary key. 
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Construct the end primary key. 
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        System.out.println("All called numbers forwarded by the base station" + strBaseStationNum + "that start from" + strStartTime + "are listed:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue to read data. 
            if (getRangeResponse.getNextStartPrimaryKey() != null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

-   Query the rows where the value of BaseStationNumber is 003 and the value of StartTime ranges from 1532574861 to 1532584054 and return only the values of the Duration column.

    This query must meet the conditions of BaseStationNumber and StartTime and return only the values of the Duration column. You can query IndexOnBaseStation1 used in the preceding example. Then, query the values of the Duration column from the data table.

    ```
    private static void getRowFromIndexAndMainTable(SyncClient client,
                                                    long baseStationNumber,
                                                    long startTime,
                                                    long endTime,
                                                    String colName) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX1_NAME);
    
        // Construct the start primary key. 
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Construct the end primary key. 
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(endTime));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        String strEndTime = String.format("%d", endTime);
    
        System.out.println("The duration of calls forwarded by the base station" + strBaseStationNum + "from" + strStartTime + "to" + strEndTime + "is listed:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                PrimaryKey curIndexPrimaryKey = row.getPrimaryKey();
                PrimaryKeyColumn mainCalledNumber = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_1);
                PrimaryKeyColumn callStartTime = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_2);
                PrimaryKeyBuilder mainTablePKBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, mainCalledNumber.getValue());
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, callStartTime.getValue());
                PrimaryKey mainTablePK = mainTablePKBuilder.build(); // Construct the primary key for the data table. 
    
                // Query the data table. 
                SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, mainTablePK);
                criteria.addColumnsToGet(colName); // Read the Duration column values of the data table. 
                // Set MaxVersions to 1 to read the latest version of data. 
                criteria.setMaxVersions(1);
                GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
                Row mainTableRow = getRowResponse.getRow();
    
                System.out.println(mainTableRow);
            }
    
            // If the nextStartPrimaryKey value is not null, continue to read data. 
            if (getRangeResponse.getNextStartPrimaryKey() != null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

    To make queries more efficient, you can create an index table named IndexOnBaseStation2 based on the BaseStationNumber and StartTime columns and specify the Duration column as an indexed attribute column. Then, query IndexOnBaseStation2 to obtain the expected data.

    The following table provides the information of IndexOnBaseStation2.

    |PK0|PK1|PK2|Defined0|
    |:--|:--|:--|:-------|
    |BaseStationNumber|StartTime|CellNumber|Duration|
    |001|1532574644|123456|60|
    |001|1532574714|234567|10|
    |002|1532574795|345678|5|
    |002|1532574861|345678|100|
    |003|1532574734|234567|20|
    |003|1532584054|456789|200|

    ```
    private static void getRangeFromIndexTable(SyncClient client,
                                               long baseStationNumber,
                                               long startTime,
                                               long endTime,
                                               String colName) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX2_NAME);
    
        // Construct the start primary key. 
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Construct the end primary key. 
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(endTime));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        // Specify the name of the column to read. 
        rangeRowQueryCriteria.addColumnsToGet(colName);
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        String strEndTime = String.format("%d", endTime);
    
        System.out.println("The duration of calls forwarded by the base station" + strBaseStationNum + "from" + strStartTime + "to" + strEndTime + "is listed:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue to read data. 
            if (getRangeResponse.getNextStartPrimaryKey() != null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ...
    ```

    If you do not specify Duration as an indexed attribute column, you must query the index table to obtain the primary key columns of the data table and then use the obtained primary key columns to query the data table. If you specify Duration as an indexed attribute column, Duration is stored in both the data table and the index table, which increases storage costs.

-   Query the total, average, maximum, and minimum call duration of all phone calls forwarded by the 003 base station. The phone call start time ranges from 1532574861 to 1532584054.

    In this query, you want to obtain the statistics for the duration of all phone calls instead of the duration of each call queried in the preceding example. You can obtain results by using the same method as in the preceding example. Then, you can perform calculations on the Duration column to obtain the required results. You can also use Data Lake Analytics \(DLA\) to obtain the required results by executing SQL statements without client-side calculations.

    **Note:** For more information about how to use DLA, see [OLAP on Tablestore: Serverless SQL big data analysis based on Data Lake Analytics](https://yq.aliyun.com/articles/618501?spm=a2c4e.11155435.0.0.701733127JMFla). DLA supports most MySQL syntax and can be used to perform complicated calculations that are applicable to your business.


## Local secondary index

You can use the local secondary index feature to perform various queries on a data table of phone calls.

The following data table records the information about phone calls. When a phone call is complete, the information about the phone call is recorded in the data table.

-   The CellNumber and StartTime columns act as the primary key columns of the data table. CellNumber indicates the caller, and StartTime the call start time.
-   CalledNumber, Duration, and BaseStationNumber act as the predefined columns of the data table. CalledNumber indicates the number of a call recipient, Duration the duration of the call, and BaseStationNumber the base station number.

|CellNumber|StartTime \(UNIX timestamp\)|CalledNumber|Duration|BaseStationNumber|
|----------|----------------------------|------------|--------|-----------------|
|123456|1532574644|654321|60|1|
|123456|1532574704|236789|60|1|
|234567|1532574714|765432|10|1|
|234567|1532574734|123456|20|3|
|345678|1532574795|123456|5|2|
|345678|1532574861|123456|100|2|
|456789|1532584054|345678|200|3|
|456789|1532585054|123456|200|3|
|456789|1532586054|234567|200|3|
|456789|1532587054|123456|200|3|

You can use the local secondary index feature to create an index table named LocalIndexOnBeCalledNumber based on CalledNumber. Then, you can query the records of phone calls between called numbers and caller numbers.

The following table provides the information of LocalIndexOnBeCalledNumber.

**Note:** Tablestore automatically adds the primary key columns that are not specified as indexed columns to an index table. The primary key columns of the data table are added to the index table and act as the primary key columns in the index table. Therefore, the index table contains three primary key columns.

|PK0|Defined0|PK1|Defined1|Defined2|
|---|--------|---|--------|--------|
|CellNumber|CalledNumber|StartTime \(UNIX timestamp\)|Duration|BaseStationNumber|
|123456|236789|1532574704|60|1|
|123456|654321|1532574644|60|1|
|234567|123456|1532574734|20|3|
|234567|765432|1532574714|10|1|
|345678|123456|1532574795|5|2|
|345678|123456|1532574861|100|2|
|456789|123456|1532585054|200|3|
|456789|123456|1532587054|200|3|
|456789|234567|1532586054|200|3|
|456789|345678|1532584054|200|3|

To query the records of phone calls between caller number 456789 and called number 123456, you need only to specify the following values when you call the getRange operation: Set the maximum and minimum values of CellNumber in the PK0 column to 456789. Set the maximum and minimum values of CalledNumber in the Defined0 column to 123456. Set the minimum value of StartTime in the PK1 column to 0 and the maximum value to INT\_MAX. Then, scan the data table to obtain the expected data. The following code provides an example on how to list the records of phone calls:

```
private static void getRangeFromMainTable(SyncClient client, long cellNumber, long calledNumber){

    RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);

    // Construct the start primary key. 
    PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_0, PrimaryKeyValue.fromLong(cellNumber));
    startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_0, PrimaryKeyValue.fromLong(calledNumber));
    startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.fromLong(0));
    rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());

    // Construct the end primary key. 
    PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_0, PrimaryKeyValue.fromLong(cellNumber));
    endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_0, PrimaryKeyValue.fromLong(calledNumber));
    endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
    rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());

    rangeRowQueryCriteria.setMaxVersions(1);

    String strNum = String.format("%d", cellNumber);
  String strCalledNum = String.format("%d", calledNumber);
    System.out.println("All records of phone calls between the caller number" + strNum + "and the called number" +strCalledNum+ "are listed:");
    while (true) {
        GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
        for (Row row : getRangeResponse.getRows()) {
            System.out.println(row);
        }

        // If the nextStartPrimaryKey value is not null, continue to read data. 
        if (getRangeResponse.getNextStartPrimaryKey() != null) {
            rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
        } else {
            break;
        }
    }
}
```

