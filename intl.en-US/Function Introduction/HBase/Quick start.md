# Quick start

This topic describes how to use Tablestore HBase Client to implement a simple program.

**Note:** This sample program uses HBase operations to access Tablestore. The complete sample program is stored in the [Aliyun Tablestore HBase client for Java](https://github.com/aliyun/aliyun-tablestore-hbase-client) project in GitHub. The directory of the sample program is src/test/java/samples/HelloWorld.java.

## Procedure

1.  Configure project dependencies

    Configure the following Maven dependencies:

    ```
       <dependencies>
            <dependency>
                <groupId>com.aliyun.openservices</groupId>
                <artifactId>tablestore-hbase-client</artifactId>
                <version>1.2.0</version>
            </dependency>
        </dependencies>
                                
    ```

    For more information about dependency configurations, see [Migrate data from HBase to Tablestore](/intl.en-US/Function Introduction/HBase/Migrate data from HBase to Tablestore.md).

2.  Configure files

    Add the following configurations to hbase-site.xml:

    ```
    <configuration>
        <property>
            <name>hbase.client.connection.impl</name>
            <value>com.alicloud.tablestore.hbase.TablestoreConnection</value>
        </property>
        <property>
            <name>tablestore.client.endpoint</name>
            <value>endpoint</value>
        </property>
        <property>
            <name>tablestore.client.instancename</name>
            <value>instance_name</value>
        </property>
        <property>
            <name>tablestore.client.accesskeyid</name>
            <value>access_key_id</value>
        </property>
        <property>
            <name>tablestore.client.accesskeysecret</name>
            <value>access_key_secret</value>
        </property>
        <property>
            <name>hbase.client.tablestore.family</name>
            <value>f1</value>
        </property>
        <property>
            <name>hbase.client.tablestore.table</name>
            <value>ots_adaptor</value>
        </property>
    </configuration>
                                
    ```

    For more information about configurations, see [Migrate data from HBase to Tablestore](/intl.en-US/Function Introduction/HBase/Migrate data from HBase to Tablestore.md).

3.  Connect to Tablestore

    Create a TableStoreConnection object to connect to Tablestore.

    ```
            Configuration config = HBaseConfiguration.create();
    
            // Create a Tablestore Connection
            Connection connection = ConnectionFactory.createConnection(config);
    
            // Admin is used for creation, management, and deletion
            Admin admin = connection.getAdmin();
                                
    ```

4.  Create a table

    Create a table by using the specified table name. Use the default MaxVersion and TimeToLive values.

    ```
            // Create an HTableDescriptor, which contains only one ColumnFamily.
            HTableDescriptor descriptor = new HTableDescriptor(TableName.valueOf(TABLE_NAME));
    
            // Create a ColumnFamily. Use the default MaxVersion and TimeToLive values. The default value of MaxVersion is 1. The default value of TimeToLive is Integer.INF_MAX.
            descriptor.addFamily(new HColumnDescriptor(COLUMN_FAMILY_NAME));
    
            // Use the createTable operation of the Admin to create a table.
            System.out.println("Create table " + descriptor.getNameAsString());
            admin.createTable(descriptor);
                                
    ```

5.  Write data

    The following code provides an example on how to write a row of data to Tablestore.

    ```
            // Create a TablestoreTable to perform operations such as read, write, update, and deletion on a single table.
            Table table = connection.getTable(TableName.valueOf(TABLE_NAME));
    
            // Create a Put object and use row_1 as the primary key.
            System.out.println("Write one row to the table");
            Put put = new Put(ROW_KEY);
    
            // Add a column. Tablestore supports only single ColumnFamilies. The ColumnFamily name is configured in hbase-site.xml. If the ColumnFamily name is not configured, the default name "f" is used. The value of COLUMN_FAMILY_NAME may be null when data is written.
            put.addColumn(COLUMN_FAMILY_NAME, COLUMN_NAME, COLUMN_VALUE);
    
            // Run put for Table, and use HBase operations to write the row of data to Tablestore
            table.put(put);
                                
    ```

6.  Read data

    The following code provides an example on how to read data of a specified row.

    ```
            // Create a Get object to read the row whose primary key is ROW_KEY.
            Result getResult = table.get(new Get(ROW_KEY));
            Result result = table.get(get);
    
            // Display the results.
            String value = Bytes.toString(getResult.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME));
            System.out.println("Get one row by row key");
            System.out.printf("\t%s = %s\n", Bytes.toString(ROW_KEY), value);
                                
    ```

7.  Scan data

    The following code provides an example on how to read data within a specified range:

    ```
        Scan data of all rows in the table.
        System.out.println("Scan for all rows:");
        Scan scan = new Scan();
        ResultScanner scanner = table.getScanner(scan);
    
        // Print the results cyclically.
        for (Result row : scanner) {
            byte[] valueBytes = row.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME);
            System.out.println('\t' + Bytes.toString(valueBytes));
        }
                                
    ```

8.  Delete a table

    The following code provides an example on how to use Admin operations to delete a table.

    ```
            print("Delete the table");
            admin.disableTable(table.getName());
            admin.deleteTable(table.getName());
                                
    ```


## Complete code

```
package samples;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.*;
import org.apache.hadoop.hbase.util.Bytes;
import java.io.IOException;
public class HelloWorld {
    private static final byte[] TABLE_NAME = Bytes.toBytes("HelloTablestore");
    private static final byte[] ROW_KEY = Bytes.toBytes("row_1");
    private static final byte[] COLUMN_FAMILY_NAME = Bytes.toBytes("f");
    private static final byte[] COLUMN_NAME = Bytes.toBytes("col_1");
    private static final byte[] COLUMN_VALUE = Bytes.toBytes("col_value");
    public static void main(String[] args) {
        helloWorld();
    }
    private static void helloWorld() {
        try  {
            Configuration config = HBaseConfiguration.create();
            Connection connection = ConnectionFactory.createConnection(config);
            Admin admin = connection.getAdmin();
            HTableDescriptor descriptor = new HTableDescriptor(TableName.valueOf(TABLE_NAME));
            descriptor.addFamily(new HColumnDescriptor(COLUMN_FAMILY_NAME));
            System.out.println("Create table " + descriptor.getNameAsString());
            admin.createTable(descriptor);
            Table table = connection.getTable(TableName.valueOf(TABLE_NAME));
            System.out.println("Write one row to the table");
            Put put = new Put(ROW_KEY);
            put.addColumn(COLUMN_FAMILY_NAME, COLUMN_NAME, COLUMN_VALUE);
            table.put(put);
            Result getResult = table.get(new Get(ROW_KEY));
            String value = Bytes.toString(getResult.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME));
            System.out.println("Get a one row by row key");
            System.out.printf("\t%s = %s\n", Bytes.toString(ROW_KEY), value);
            Scan scan = new Scan();
            System.out.println("Scan for all rows:");
            ResultScanner scanner = table.getScanner(scan);
            for (Result row : scanner) {
                byte[] valueBytes = row.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME);
                System.out.println('\t' + Bytes.toString(valueBytes));
            }
            System.out.println("Delete the table");
            admin.disableTable(table.getName());
            admin.deleteTable(table.getName());
            table.close();
            admin.close();
            connection.close();
        } catch (IOException e) {
            System.err.println("Exception while running HelloTablestore: " + e.toString());
            System.exit(1);
        }
    }
}
            
```

