# Configure conditional update

If you use conditional update, data in the table can be updated only when the conditions are met. If the conditions are not met, the update fails.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Java SDK/Initialization.md).
-   A data table is created and data is written to the table.

## Usage notes

When you use the PutRow, UpdateRow, DeleteRow, or BatchWriteRow operation to update data, you can use conditional update to check row existence conditions and column-based conditions. The update is successful only when the conditions are met.

Conditional update includes row existence conditions and column-based conditions.

-   Row existence conditions: include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST.

    When you modify a table, Tablestore first checks the row existence condition. If the row existence condition is not met, the modification fails, and an error is reported.

-   Column-based conditions: include SingleColumnValueCondition and CompositeColumnValueCondition, which are used to determine whether the conditions are met based on the values of one or more columns.
    -   SingleColumnValueCondition supports the comparison between a constant and a column that can be a primary key column. SingleColumnValueCondition does not support the comparison between two columns or two constants.
    -   The inner node of CompositeColumnValueCondition is used to perform logical operations. Subconditions can be SingleColumnValueCondition or CompositeColumnValueCondition.

Conditional update can be used to implement optimistic locking. When you update a row, the value of the specified column is obtained. For example, Column A has a value of 1. Obtain the value in Column A and set a condition that the value in Column A is 1. Update the value in Column A to 2. If the update fails, the row is updated by another client.

## Limits

Column-based conditions for conditional update support relational operators such as =, !=, \>, \>=, <, and <=, and logical operators such as NOT, AND, and OR. You can specify up to 10 conditions.

## Parameters

|Parameter|Description|
|---------|-----------|
|RowExistenceExpectation|When you modify a table, Tablestore first checks the row existence condition. If the row existence condition is not met, the modification fails, and an error is reported.Row existence conditions include IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST.

-   IGNORE: No existence check is performed.
-   EXPECT\_EXIST: The row is expected to exist. If the row exists, the condition is met. If the row does not exist, the condition is not met.
-   EXPECT\_NOT\_EXIST: Expect that the row does not exist. If the row does not exist, the condition is met. If the row exists, the condition is not met. |
|ColumnName|The name of the column.|
|ColumnValue|The comparison value of the column.|
|CompareOperator|The relational operator that is used to compare column values. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).Relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). |
|LogicOperator|The logical operator that is used to combine multiple conditions. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).Logical operators include NOT, AND, and OR.

Different logical operators require different number of subconditions.

-   When the logical operator is NOT, only one subcondition can be added.
-   When the logical operator is AND or OR, you must add at least two subconditions. |
|PassIfMissing|Specifies whether to pass the conditional check when a column does not exist in a row. The type of this parameter value is Boolean. The default value is true, which indicates that if the column does not exist in a row, the conditional check is passed, and the row meets the update conditions.When PassIfMissing is set to false, if the column does not exist in a row, the conditional check fails, and the row does not meet the update conditions. |
|LatestVersionsOnly|Specifies whether to use only the value of the latest version when the column has multiple versions of values. The type of this parameter value is Boolean. The default value is true, which indicates that if the column has multiple versions of data, only the value of the latest version is used for comparison.When LatestVersionsOnly is set to false, the column values of all versions are used for comparison if the column has multiple versions of data. In this case, the conditional check is passed when at least one version of value meets the condition, and the row meets the update conditions. |

## Examples

The following code provides examples on how to use column-based judgment conditions and implement optimistic locking:

-   Construct a SingleColumnValueCondition.

    ```
     // Configure the condition: Col0==0.
     SingleColumnValueCondition singleColumnValueCondition = new SingleColumnValueCondition("Col0",
             SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(0));
     // If Col0 does not exist, the condition is not met.
     singleColumnValueCondition.setPassIfMissing(false);
     // Specify that only the latest version is used for comparison.
     singleColumnValueCondition.setLatestVersionsOnly(true);
                        
    ```

-   Construct a CompositeColumnValueCondition.

    ```
     // Set condition composite1 to (Col0 == 0) AND (Col1 > 100).
     CompositeColumnValueCondition composite1 = new CompositeColumnValueCondition(CompositeColumnValueCondition.LogicOperator.AND);
     SingleColumnValueCondition single1 = new SingleColumnValueCondition("Col0",
             SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(0));
     SingleColumnValueCondition single2 = new SingleColumnValueCondition("Col1",
             SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100));
     composite1.addCondition(single1);
     composite1.addCondition(single2);
    
     // Set condition composite2 to ( (Col0 == 0) AND (Col1 > 100) ) OR (Col2 <= 10).
     CompositeColumnValueCondition composite2 = new CompositeColumnValueCondition(CompositeColumnValueCondition.LogicOperator.OR);
     SingleColumnValueCondition single3 = new SingleColumnValueCondition("Col2",
             SingleColumnValueCondition.CompareOperator.LESS_EQUAL, ColumnValue.fromLong(10));
     composite2.addCondition(composite1);
     composite2.addCondition(single3);     
                        
    ```

-   The following code provides an example on how to implement optimistic locking by increasing the values of a column:

    ```
     private static void updateRowWithCondition(SyncClient client, String pkValue) {
         // Create the primary key.
         PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
         primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
         PrimaryKey primaryKey = primaryKeyBuilder.build();
    
         // Read a row of data.
         SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
         criteria.setMaxVersions(1);
         GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
         Row row = getRowResponse.getRow();
         long col0Value = row.getLatestColumn("Col0").getValue().asLong();
    
         // Configure the conditional update to increment the value of Col0 by 1.
         RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, primaryKey);
         Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
         ColumnCondition columnCondition = new SingleColumnValueCondition("Col0", SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(col0Value));
         condition.setColumnCondition(columnCondition);
         rowUpdateChange.setCondition(condition);
         rowUpdateChange.put(new Column("Col0", ColumnValue.fromLong(col0Value + 1)));
    
         try {
             client.updateRow(new UpdateRowRequest(rowUpdateChange));
         } catch (TableStoreException ex) {
             System.out.println(ex.toString());
         }
     }
                        
    ```


