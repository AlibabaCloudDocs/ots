# Filter

Tablestore provides filter to filter the results on the server and return the rows that match the filter conditions. After results are filtered, only rows of data that matches the filter condition are returned.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).
-   A table is created. Data is written to the table.

## Usage notes

When you call the GetRow, BatchGetRow, or GetRange operation to query data, you can use a filter to return only the rows that meet the filter conditions.

Filters include SingleColumnCondition and CompositeColumnCondition.

-   SingleColumnCondition: determines whether to filter a row based only on the values of a reference column.
-   CompositeColumnCondition: determines whether to filter a row based on a combination of the filter conditions on values of multiple reference columns.

## Limits

-   A filter can consist of up to 10 conditions. Supported conditions include relational operator-based operations \(=, ! =, \>, \>=, <, and <=\) and logical operations \(NOT, AND, and OR\).
-   The reference columns used by a filter must be included in the read data. If the specified columns from which data is read do not include reference columns, the filter cannot query the values of reference columns.
-   You can use filters by calling the GetRow, BatchGetRow, and GetRange operations, which does not change the native semantics or limited items of these operations.

    When you use GetRange, the number of rows scanned cannot exceed 5,000, or the data scanned cannot exceed 4 MB in size.

    If the scanned 5,000 rows or 4 MB of data does not match the filter conditions, the rows in the response are empty. However, next\_start\_primary\_key may not be empty. In this case, you must use next\_start\_primary\_key to continue reading the data until next\_start\_primary\_key is empty.


## Parameters

|Parameter|Description|
|---------|-----------|
|column\_name|The name of the reference column used by the filter.|
|column\_value|The value of the reference column used by the filter.|
|ComparatorType|The relational operator in the filter. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).The relational operators include EQUAL \(=\), NOT\_EQUAL \(! =\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). |
|LogicOperator|The logical operator in the filter. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).The logical operators include NOT, AND, and OR. |
|pass\_if\_missing|Specifies whether to return a row if the row does not contain the reference column. The data type of this parameter value is Boolean. The default value is True, which indicates that if the reference column does not exist in a row, the row is returned.When pass\_if\_missing is set to False, if the reference column does not exist in a row, the row is not returned. |
|latest\_version\_only|Specifies whether to use only the value of the latest version for comparison when the reference column contains data of multiple versions. The data type of this parameter value is Boolean. The default value is True, which indicates that if the reference column has multiple versions of data, only the value of the latest version is used for comparison.When latest\_version\_only is set to False, if a reference column has multiple versions of data, the values of all versions in the column are used for comparison. In this case, when at least one version of data meets the conditions, the row is returned. |

## Examples

-   The following code provides an example on how to create SingleColumnCondition:

    ```
    def get_row_with_condition(client):
        primary_key = [('uid',1), ('gid',101)]
        columns_to_get = [] # Set the columns to return. If this parameter is not set, all columns are returned.
        // Specify that a row is returned when the value of name is Hangzhou.
        cond = SingleColumnCondition("name", 'Hangzhou', ComparatorType.EQUAL, pass_if_missing = True)
        consumed, return_row, next_token = client.get_row(table_name, primary_key, columns_to_get, cond, 1)
    
        print ('Read succeed, consume %s read cu.' % consumed.read)
    
        print ('Value of primary key: %s' % return_row.primary_key)
        print ('Value of attribute: %s' % return_row.attribute_columns)
        for att in return_row.attribute_columns:
            print ('name:%s\tvalue:%s\ttimestamp:%d' % (att[0], att[1], att[2]))
    ```

-   The following code provides an example on how to create CompositeColumnCondition:

    ```
    def get_row_with_composite_condition(client):
        primary_key = [('uid',1), ('gid',101)]
        columns_to_get = [] # given a list of columns to get, or empty list if you want to get entire row.
        // Specify that a row returned when the value of growth is 0.9, and the value of name is Hangzhou.
        cond = CompositeColumnCondition(LogicalOperator.AND)
        cond.add_sub_condition(SingleColumnCondition("growth", 0.9, ComparatorType.EQUAL))
        cond.add_sub_condition(SingleColumnCondition("name", 'Hangzhou', ComparatorType.EQUAL))
    
        consumed, return_row, next_token = client.get_row(table_name, primary_key, columns_to_get, cond, 1)
    
        print ('Read succeed, consume %s read cu.' % consumed.read)
    
        print ('Value of primary key: %s' % return_row.primary_key)
        print ('Value of attribute: %s' % return_row.attribute_columns)
        for att in return_row.attribute_columns:
            print ('name:%s\tvalue:%s\ttimestamp:%d' % (att[0], att[1], att[2]))
    ```


