# Conditional update

If you use conditional update, data in the table can be updated only when the conditions are met. If the conditions are not met, the update fails.

## Scenarios

Conditional update applies to scenarios where high-concurrency applications are updated.

In these scenarios, old\_value may be updated by other clients. If you use conditional update, the current value is updated to new\_value only when the current value is equal to old\_value.

**Note:** In scenarios where concurrency is high, such as web page counting or games, data updates may fail if you use conditional update. In these cases, you can retry the data update.

```
 // Obtain the current value.
 old_value = Read();
 // Perform calculations on the current value, such as add 1 to the current value.
 new_value = func(old_value);
 // Use the new value to update the current value.
 Update(new_value)；
                
```

## Operations

Conditional update supports relational operator-based operations including =, !, =, \>, \>=, <, and <= and logical operations including NOT, AND, and OR. You can use a combination of up to 10 conditions in an update operation. You can use conditional update in the [PutRow](/intl.en-US/API Reference/Operations/PutRow.md), [UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md), [DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md), and [BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md) operations.

Conditional update can be used to implement optimistic locking. When you update a row, the value of the specified column is obtained. For example, Column A has a value of 1. Obtain the value in Column A and set a condition that the value of Column A is 1. Update the value of Column A to 2. If the row is updated by another client, the update fails.

Column-based judgment conditions include column-based conditions and the row existence condition.

|Column-based judgment condition|Description|
|-------------------------------|-----------|
|Column-based condition|Column-based conditions support SingleColumnValueCondition and CompositeColumnValueCondition, which are used to perform the condition-based judgment based on the values of one or more columns. Column-based conditions are similar to the conditions used by Tablestore filters.|
|[Row existence condition](/intl.en-US/API Reference/Data Types/Condition.md)|When you modify a table, Tablestore first checks the row existence condition. If the row existence condition is not met, the modification fails and an error is reported. The row existence condition is classified into the following types:

-   IGNORE
-   EXPECT\_EXIST
-   EXPECT\_NOT\_EXIST

For more information, see [Row existence condition-based update rules](#p_475_9wy_nbk). |

Row existence condition-based update rules

**Note:** BatchWriteRow is a set of multiple PutRow, UpdateRow, and DeleteRow operations. When you manage data in the table by using the [BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md) operation, see the update rules of the corresponding operation based on the operation type.

|Operation|IGNORE|EXPECT\_EXIST|EXPECT\_NOT\_EXIST|
|:--------|:-----|:------------|:-----------------|
|[PutRow](/intl.en-US/API Reference/Operations/PutRow.md): The row exists.|Succeed|Succeed|Fail|
|[PutRow](/intl.en-US/API Reference/Operations/PutRow.md): The row does not exist.|Succeed|Fail|Succeed|
|[UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md): The row exists.|Succeed|Succeed|Fail|
|[UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md): The row does not exist.|Succeed|Fail|Succeed|
|[DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md): The row exists.|Succeed|Succeed|Fail|
|[DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md): The row does not exist.|Succeed|Fail|Succeed|

## Use Tablestore SDKs

You can use the following Tablestore SDKs to implement conditional update:

-   Tablestore SDK for Java: [Conditional update](/intl.en-US/SDK Reference/Java SDK/Table operations/Configure conditional update.md)
-   Tablestore SDK for Go: [Conditional update](/intl.en-US/SDK Reference/Go SDK/Table operations/Configure conditional update.md)
-   Tablestore SDK for Python: [Conditional update](/intl.en-US/SDK Reference/Python SDK/Table operations/Configure conditional update.md)
-   Tablestore SDK for Node.js: [Conditional update](/intl.en-US/SDK Reference/NodeJS SDK/Table operations/Configure conditional update.md)
-   Tablestore SDK for .NET: [Conditional update](/intl.en-US/SDK Reference/.NET SDK/Table operations/Configure conditional update.md)
-   Tablestore SDK for PHP: [Conditional update](/intl.en-US/SDK Reference/PHP SDK/Table operations/Configure conditional update.md)

## Examples

The following code provides examples on how to use column-based judgment conditions and implement optimistic locking:

-   Construct a SinglleColumnValueCondition.

    ```
     // Set the condition: Col0==0.
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

-   The following code provides an example on how to implement optimistic locking by increasing the value of a column:

    ```
     private static void updateRowWithCondition(SyncClient client, String pkValue) {
         // Construct the primary key.
         PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
         primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
         PrimaryKey primaryKey = primaryKeyBuilder.build();
    
         // Read a row of data.
         SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
         criteria.setMaxVersions(1);
         GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
         Row row = getRowResponse.getRow();
         long col0Value = row.getLatestColumn("Col0").getValue().asLong();
    
         // Configure conditional update to increase the value of Col0 by 1.
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


## Billing methods

The calculation of capacity units \(CUs\) is not affected if the data is written or updated. However, if the conditional update fails, a write CU and a read CU are consumed.

