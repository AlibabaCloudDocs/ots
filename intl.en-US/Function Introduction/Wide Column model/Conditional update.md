# Conditional update

If you use conditional update, data in a table can be updated only when the conditions are met. If the conditions are not met, the update fails.

## Scenario

Conditional update is suitable for scenarios where you need to update high concurrency applications.

In these scenarios, old\_value may be updated by other clients. If you use conditional update, the current value is updated to new\_value only when the current value is equal to old\_value.

**Note:** If you use conditional update in high-concurrency scenarios such as web page counting or games, data updates may fail. In this case, update the data again multiple times.

```
 // Obtain the current value.
 old_value = Read();
 // Perform calculations on the current value. In this case, add 1 to the current value.
 new_value = func(old_value);
 // Update the current value to the new value.
 Update(new_value)；
                
```

## API operations

Conditional update supports relational operators including =, !, =, \>, \>=, <, and <= and logic operators including NOT, AND, and OR. You can use a combination of up to 10 conditions in an update operation. You can use conditional update in the [PutRow](/intl.en-US/API Reference/Operations/PutRow.md), [UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md), [DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md), and [BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md) operations.

Conditional update can be used to implement optimistic locking. When you update a row, the value in the specified column is obtained. Assume that Column A has a value of 1. Obtain the value in Column A and set a condition that the value in Column A is 1. Update the value in Column A to 2. If the update fails, the row is updated by another client.

Column-based judgment conditions include column-based conditions and the row existence condition.

|Column-based judgment condition|Description|
|-------------------------------|-----------|
|Column-based condition|Column-based conditions support SingleColumnValueCondition and CompositeColumnValueCondition, which are used to perform the condition-based judgment based on the values of one or more columns. Column-based conditions are similar to the conditions used by Tablestore filters.|
|[Row existence condition](/intl.en-US/API Reference/Data Types/Condition.md)|When you modify a table, the system first checks the row existence condition. If the row existence condition is not met, the modification fails, and an error is reported. The row existence condition is classified into the following types:

-   IGNORE
-   EXPECT\_EXIST
-   EXPECT\_NOT\_EXIST

For more information, see the following [Row existence condition update rules](#p_475_9wy_nbk) section. |

Row existence condition update rules

**Note:** BatchWriteRow is a set of multiple PutRow, UpdateRow, and DeleteRow operations. When you use the [BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md) operation to manage data in a table, see the update rules of corresponding operations based on the operation type.

|API operation|IGNORE|EXPECT\_EXIST|EXPECT\_NOT\_EXIST|
|:------------|:-----|:------------|:-----------------|
|[PutRow](/intl.en-US/API Reference/Operations/PutRow.md): The row exists.|Succeed|Succeed|Fail|
|[PutRow](/intl.en-US/API Reference/Operations/PutRow.md): The row does not exist.|Succeed|Fail|Succeed|
|[UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md): The row exists.|Succeed|Succeed|Fail|
|[UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md): The row does not exist.|Succeed|Fail|Succeed|
|[DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md): The row exists.|Succeed|Succeed|Fail|
|[DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md): The row does not exist.|Succeed|Fail|Succeed|

## Usage

You can use the following SDKs to implement conditional update:

-   SDK for Java: [Conditional update](/intl.en-US/SDK Reference/Java SDK/Table operations/Configure conditional update.md)
-   SDK for Go: [Conditional update](/intl.en-US/SDK Reference/Go SDK/Table operations/Configure conditional update.md)
-   SDK for Python: [Conditional update](/intl.en-US/SDK Reference/Python SDK/Table operations/Configure conditional update.md)
-   SDK for Node.js: [Conditional update](/intl.en-US/SDK Reference/NodeJS SDK/Table operations/Configure conditional update.md)
-   SDK for .NET: [Conditional update](/intl.en-US/SDK Reference/.NET SDK/Table operations/Configure conditional update.md)
-   SDK for PHP: [Conditional update](/intl.en-US/SDK Reference/PHP SDK/Table operations/Configure conditional update.md)

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


## Billing methods

The calculation of capacity units \(CUs\) is not affected if data is written or updated. However, if the conditional update fails, a write CU and a read CU are consumed.

