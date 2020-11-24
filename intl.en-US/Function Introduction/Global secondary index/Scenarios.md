# Scenarios

Global secondary index allows you to create an index table based on specified columns. Data in the generated index table is sorted by the specified indexed columns. All data written to the base table is automatically synchronized to the index table in an asynchronous manner. If you only write data to a base table and query index tables created on the base table, the query performance can be improved in most scenarios.

You can use global secondary index to perform various queries on a table of phone calls.

The following table is a record of phone calls. When a phone call is completed, the information about the phone call is recorded in the table.

-   The CellNumber and StartTime columns act as the primary key. CellNumber represents the caller, and StartTime the call start time.
-   CalledNumber represents the call recipient, Duration the duration time of the call, and BaseStationNumber the base station number.

|CellNumber|StartTime \(Unix timestamps\)|CalledNumber|Duration|BaseStationNumber|
|:---------|:----------------------------|:-----------|:-------|:----------------|
|123456|1532574644|654321|60|1|
|234567|1532574714|765432|10|1|
|234567|1532574734|123456|20|3|
|345678|1532574795|123456|5|2|
|345678|1532574861|123456|100|2|
|456789|1532584054|345678|200|3|

You can create an index table based on the CalledNumber and BaseStationNumber columns to perform various queries. For the sample code that is used to create index tables, see [Appendix](/intl.en-US/Function Introduction/Global secondary index/Appendix.md).

You can use global secondary index to meet the following query requirements:

-   Query the rows where the value of CellNumber is 234567.

    Tablestore sorts rows in a table based on their primary keys and provides the getRange operation. You can call the getRange operation to query the rows where the value of CellNumber is 234567. Set both the maximum and minimum values of CellNumber to 234567, and set the minimum value of StartTime to 0 and the maximum value to INT\_MAX. Then, scan the table to obtain the expected data.

    ```
    private static void getRangeFromMainTable(SyncClient client, long cellNumber)
    {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
    
        // Specify the primary key from which the query starts.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(0));
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Specify the primary key at which the query ends.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strNum = String.format("%d", cellNumber);
        System.out.println("The caller" + strNum + "makes the following calls:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue the read operation.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

-   Query the rows where the value of CalledNumber is 123456.

    Tablestore sorts rows in a table based on their primary keys. Queries that involve predefined columns such as the CalledNumber column in this table are slow and inefficient. You can create an index table called IndexOnBeCalledNumber based on the CalledNumber column. CalledNumber becomes the primary key column of the index table. Therefore, you can call the getRange operation to scan the index table to obtain the expected data.

    The following table provides the information of IndexOnBeCalledNumber.

    **Note:** Tablestore automatically adds all primary key columns of the base table to the index table. The primary key columns of the base table are added to the index table and act as the primary key columns in the index table. Therefore, the index table contains three primary key columns.

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
    
        // Specify the primary key from which the query starts.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Specify the primary key at which the query ends.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strNum = String.format("%d", cellNumber);
        System.out.println("The recipient" + strNum + "was called by the following numbers:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue the read operation.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

-   Query the rows where the value of BaseStationNumber is 002 and the value of StartTime is 1532574740.

    This query is similar to the query in the preceding example but includes two query conditions, BaseStationNumber and StartTime. You can create an index table named IndexOnBaseStation1 based on BaseStationNumber and StartTime. Then, you can query IndexOnBaseStation1 to obtain the expected data.

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
    
        // Specify the primary key from which the query starts.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Specify the primary key at which the query ends.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        System.out.println("The following caller numbers are forwarded by the base station" + strBaseStationNum + "at the start time" + strStartTime + ":");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue the read operation.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

-   Query the rows where the value of BaseStationNumber is 003 and the value of StartTime ranges from 1532574861 to 1532584054 and return only the values of the Duration column.

    This query must meet the conditions of BaseStationNumber and StartTime and return only the values of the Duration column. You can query IndexOnBaseStation1 used in the preceding example. Then, query the base table for the values of the Duration column.

    ```
    private static void getRowFromIndexAndMainTable(SyncClient client,
                                                    long baseStationNumber,
                                                    long startTime,
                                                    long endTime,
                                                    String colName) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX1_NAME);
    
        // Specify the primary key from which the query starts.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Specify the primary key at which the query ends.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(endTime));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        String strEndTime = String.format("%d", endTime);
    
        System.out.println("The following values are the duration of calls forwarded by the base station" + strBaseStationNum + "from" + strStartTime + "to" + strEndTime + ":");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                PrimaryKey curIndexPrimaryKey = row.getPrimaryKey();
                PrimaryKeyColumn mainCalledNumber = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_1);
                PrimaryKeyColumn callStartTime = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_2);
                PrimaryKeyBuilder mainTablePKBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, mainCalledNumber.getValue());
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, callStartTime.getValue());
                PrimaryKey mainTablePK = mainTablePKBuilder.build(); // Specify primary keys for the base table.
    
                // Query the base table.
                SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, mainTablePK);
                criteria.addColumnsToGet(colName); // Read the Duration column values of the base table.
                // Set MaxVersions to 1 to read the latest version of data.
                criteria.setMaxVersions(1);
                GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
                Row mainTableRow = getRowResponse.getRow();
    
                System.out.println(mainTableRow);
            }
    
            // If the nextStartPrimaryKey value is not null, continue the read operation.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

    To improve query efficiency, you can create an index table named IndexOnBaseStation2 based on the BaseStationNumber and StartTime columns and specify the Duration column as an indexed attribute column. Then, query IndexOnBaseStation2 to obtain the expected data.

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
    
        // Specify the primary key from which the query starts.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // Specify the primary key at which the query ends.
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
    
        System.out.println("The following values are the duration of calls forwarded by the base station" + strBaseStationNum + "from" + strStartTime + "to" + strEndTime + ":");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, continue the read operation.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ...
    ```

    If you do not set Duration as an indexed attribute column, you must query the index table to obtain the primary keys of the expected data, and then use the obtained primary keys to query the base table. If you set Duration as an indexed attribute column, Duration is stored in both the base table and the index table, which increases the storage cost.

-   Query the total call duration, average call duration, maximum call duration, and minimum call duration of all calls forwarded by the 003 base station and whose call start time ranges from 1532574861 to 1532584054.

    In this query, you want to obtain the statistics about the duration of all phone calls instead of the duration of each call queried in the preceding example. You can obtain results by using the same method as in the preceding example. Then, you can perform calculations on the Duration column to obtain the required result. You can also use Data Lake Analytics to obtain the required result by executing SQL statements without client-side calculations.

    **Note:** For more information about how to use Data Lake Analytics, see [OLAP on Tablestore: Serverless SQL big data analysis based on Data Lake Analytics](https://yq.aliyun.com/articles/618501?spm=a2c4e.11155435.0.0.701733127JMFla). Data Lake Analytics supports most MySQL syntax and can perform complicated calculations that are applicable to your business.


