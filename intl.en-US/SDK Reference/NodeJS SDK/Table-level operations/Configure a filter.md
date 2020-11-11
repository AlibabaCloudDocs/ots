# Configure a filter

Tablestore provides filter to filter the results on the server and return the rows that match the filter conditions. After results are filtered, only rows of data that matches the filter condition are returned.

## Prerequisites

-   Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created and data is written to the table.

## Usage notes

When you query data by calling the GetRow, BatchGetRow, or GetRange operation, you can use the filter to return only the rows that meet the filter conditions.

Filters include SingleColumnCondition and CompositeColumnCondition.

-   SingleColumnCondition: determines whether to filter a row based only on the values of a reference column.
-   CompositeColumnCondition: determines whether to filter a row by combining the filter conditions of values of multiple reference columns.

## Limits

-   A filter can consist of up to 10 conditions. Supported conditions include relational operations \(=, ! =, \>, \>=, <, and <=\) and logical operations \(NOT, AND, and OR\).
-   The reference columns used by a filter must be included in the read data. If you specify columns from which data is read that do not include the reference columns, the filters cannot obtain the reference column values.
-   The native semantics or limits of the GetRow, BatchGetRow, and GetRange operations are not affected when you use filters.

    You can scan up to 5,000 rows and return up to 4 MB of data with a single GetRange operation.

    If no data matches the filter conditions in the scanned range, the returned rows are empty. However, next\_start\_primary\_key may not be empty. In this case, you must use next\_start\_primary\_key to continue reading the data until next\_start\_primary\_key is empty.


## Parameters

|Parameter|Description|
|---------|-----------|
|columnName|The name of the reference column used by the filter.|
|columnValue|The value of the reference column used by the filter.|
|ComparatorType|The relational operator used by the filter. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).The relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUA L\(<=\). |
|LogicOperator|The logical operator used by the filter. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).The logical operators include NOT, AND, and OR. |
|passIfMissing|Specifies whether to return a row when a reference column in the row does not exist. The data type of the parameter value is Boolean. The default value is true, which indicates that if a reference column does not exist in a row, the row is returned.If you set passIfMissing to false, a row is not returned if a reference column does not exist in the row. |
|latestVersionOnly|Specifies whether to use only the latest versions of data in the reference columns for comparison when each reference column contains data of multiple versions. The data type of the parameter value is Boolean. The default value is true, which indicates that the latest versions of data are used for comparison when each reference column contains data of multiple versions.If you set latestVersionOnly to false, all versions of data in a reference column are used for comparison. The row is returned if one version of data in the reference column meets the condition. |

## Examples

-   The following code provides an example on how to construct a SingleColumnCondition:

    ```
    function getRowWithCondition() {
      // Specify that the row is returned when the value of the col1 column is Tablestore. When passIfMissing is set to true, the row is returned if this column does not exist. When passIfMissing is set to false, the row is not returned if this column does not exist.
      var condition = new TableStore.SingleColumnCondition('col1', 'Tablestore', TableStore.ComparatorType.EQUAL,true);
    
      params.columnFilter = condition;
      client.getRow(params, function (err, data) {
        if (err) {
          console.log('error:', err);
          return;
        }
        console.log('success:', data);
      });
    }
    ```

-   The following code provides an example on how to construct a CompositeCondition:

    ```
    function getRowWithCompositeCondition() {
      // Specify that the row is returned when the value of the col1 column is Tablestore, and the value of the col5 column is 123456789.
      var condition = new TableStore.CompositeCondition(TableStore.LogicalOperator.AND);
      condition.addSubCondition(new TableStore.SingleColumnCondition('col1', 'Tablestore', TableStore.ComparatorType.EQUAL));
      condition.addSubCondition(new TableStore.SingleColumnCondition('col5', Long.fromNumber(123456789), TableStore.ComparatorType.EQUAL));
    
      params.columnFilter = condition;
      client.getRow(params, function (err, data) {
        if (err) {
          console.log('error:', err);
          return;
        }
        console.log('success:', data);
      });
    }
    ```


