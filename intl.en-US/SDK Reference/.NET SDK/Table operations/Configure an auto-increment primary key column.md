# Configure an auto-increment primary key column

This topic describes how to configure an auto-increment primary key column. You cannot set a partition key to an auto-increment column. If you write data to a table that contains an auto-increment primary key column, you do not need to specify specific values for the auto-increment primary key column because Tablestore generates values for the auto-increment primary key column. Values generated for the auto-increment primary key column are unique and consecutive within the partition that shares the same partition key value.

## Prerequisites

Client is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/.NET SDK/Initialization.md).

## Usage notes

1.  When you create a table, you cannot set the partition key to an auto-increment primary key column.

    The data type of an auto-increment primary key column can only be set to integer. Each value generated for an auto-increment primary key column is a 64-bit signed long integer.

2.  When you write data to a table, you do not need to set specific values for the auto-increment column. You need only to set placeholder values for the auto-increment column.

    If you want to obtain the values of the auto-increment column after data is written to the table, you can set ReturnType to RT\_PK.

    When you query data, you must specify the values of all primary key columns. To obtain a complete primary key value, you can set ReturnType to RT\_PK in PutRow, UpdateRow, or BatchWriteRow.


## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

-   To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to AUTO\_INCREMENT.
-   When you write data to a table, you do not need to set specific values for the auto-increment primary key column. You need only to set placeholder values for the column.

```
using System;
using System.Collections.Generic;
using Aliyun.OTS.DataModel;
using Aliyun.OTS.Request;
using Aliyun.OTS.Response;

namespace Aliyun.OTS.Samples.Samples
{
    /// <summary>
    /// Example: Create an auto-increment primary key column.
    /// </summary>
    public class AutoIncrementSample
    {
        private static readonly string TableName = "AutoIncrementSample";

        private static readonly string Pk1 = "Pk1";
        private static readonly string Pk2 = "Pk2_AutoIncrement";


        static void Main(string[] args)
        {
            Console.WriteLine("AutoIncrementSample");

            // Create a table that contains an auto-increment column.
            CreateTableWithAutoIncrementPk();

            // Write 10 rows of data.
            for (int i = 0; i < 10; i++)
            {  
                PutRow(i.ToString());
            }

            Console.ReadLine();

        }

        /// <summary>
        /// Create a table that contains an auto-increment column.
        /// </summary>
        private static void CreateTableWithAutoIncrementPk()
        {

            OTSClient otsClient = Config.GetClient();

            IList<string> tables = otsClient.ListTable(new ListTableRequest()).TableNames;
            if (tables.Contains(TableName))
            {
                return;
            }

            PrimaryKeySchema primaryKeySchema = new PrimaryKeySchema
            {
                { Pk1, ColumnValueType.String },
                // Set Pk2 to the auto-increment column.
                { Pk2, ColumnValueType.Integer, PrimaryKeyOption.AUTO_INCREMENT}
            };
            TableMeta tableMeta = new TableMeta(TableName, primaryKeySchema);

            CapacityUnit reservedThroughput = new CapacityUnit(0, 0);
            CreateTableRequest request = new CreateTableRequest(tableMeta, reservedThroughput);
            otsClient.CreateTable(request);

        }

        public static void PutRow(string pk1Value)
        {
            Console.WriteLine("Start put row...") ;
            OTSClient otsClient = Config.GetClient();

            // Specify the primary keys of the row. These primary keys must be consistent with those specified in TableMeta when you create the table.
            PrimaryKey primaryKey = new PrimaryKey
            {
                { Pk1, new ColumnValue(pk1Value) },
                { Pk2,  ColumnValue.AUTO_INCREMENT }
            };

            // Specify the attribute columns of the row.
            AttributeColumns attribute = new AttributeColumns
            {
                { "Col1", new ColumnValue(0) }
            };
            PutRowRequest request = new PutRowRequest(TableName, new Condition(RowExistenceExpectation.IGNORE), primaryKey, attribute);
            request.RowPutChange.ReturnType = ReturnType.RT_PK;

            var response =  otsClient.PutRow(request);
            Console.WriteLine("Put row succeed, autoIncrement Pk value:"+ response.Row.GetPrimaryKey()[Pk2].IntegerValue);
        }

    }
}
```

