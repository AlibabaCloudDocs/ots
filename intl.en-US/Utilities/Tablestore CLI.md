# Tablestore CLI

Tablestore CLI provides simple and clear commands and supports Windows, Linux, and macOS operating systems. This topic describes how to use Tablestore CLI to manage data stored in Tablestore by using command lines.

## Download Tablestore CLI

The following table provides the download URLs of Tablestore CLI for different operating systems.

|Operating system|Download URL|
|----------------|------------|
|Windows|[Windows10](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-windows-amd64-2019-11-11.zip)|
|Linux|-   [Linux \(AMD64\)](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-linux-amd64-2019-11-11.zip)
-   [Linux \(ARM64\)](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-linux-arm64-2019-11-11.zip) |
|Mac|[Mac](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-macos-amd64-2019-11-11.zip)|

## Quick start

You can use the following commands in Tablestore CLI to perform operations on data stored in Tablestore.

```
#1 Start Tablestore CLI.
$./ts

#2 Configure the corresponding connection information and modify the fields based on your actual conditions.
ts> config --endpoint https://myinstance.cn-hangzhou.ots.aliyuncs.com --instance myinstance --id test_accessid --key test_accesskey

#3 View all tables on the current instance.
ts> lt

#4 Create a table.
ts> create -t mysampletable --pk '[{"C":"uid","t":"string"}, {"c":"pid","t":"integer"}]' --ttl 864000 --version 1

'''
Table name: mysampletable 
Two primary key columns are specified: uid (Type: String) and pid (Type: Integer)
Time to live: 864000 seconds (10 days)
Maximum number of versions: 1
By default, if the time to live and maximum number of versions of the table are not specified, only one version of data that never expire is retained.
'''

#5 View table information.
ts> desc -t mysampletable

#6 Use the table.
ts> use -t mysampletable

#7 Insert data into the table.
ts> put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen1"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6772]' --attr '[{"c":"name", "v":"redchen2"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6773]' --attr '[{"c":"name", "v":"redchen3"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6774]' --attr '[{"c":"name", "v":"redchen4"}, {"c":"country", "v":"china"}]'

#8 Read data from the table.
ts> get --pk '["86",6771]'
ts> get --pk '["86",6772]'
ts> get --pk '["86",6773]'
ts> get --pk '["86",6774]'

#9 Delete the table.
ts> drop -t mysampletable -y
```

## View supported options

You can use the help option to view all options that Tablestore CLI supports.

```
$./ts help

Commands:
  alter                  Alter table
  clear                  Clear the screen
  config                 Config the TableStore access information
  create                 Create a new table
  del                    Delete the specify row from TableStore
  desc                   Show table meta
  drop                   Drop the table
  exit                   Exit the program
  export                 Export the data of table to disk from TableStore, not support multi version
  get                    Get specify row from TableStore
  help                   Display help
  import                 Load the data to TableStore, not support multi version
  list                   List all tables
  points                 Logically divide the data of the full table into several shards close to the specified size
  press_check            Check data for press
  press_input            Input data for press
  put                    Insert a row to TableStore
  quit                   Quit the program
  update                 Insert a row to TableStore
  use                    Select table
```

## Initialize Tablestore CLI

Configure the corresponding connection information and modify the fields based on your actual conditions.

```
Sample:
    config --endpoint https://myinstance.cn-hangzhou.ots.aliyuncs.com --instance myinstance --id test_accessid --key test_accesskey
Flags:
      --endpoint Endpoint     TableStore Endpoint
      --id AccessKeyId        User AccessKeyId
      --instance Instance     TableStore Instance
      --key AccessKeySecret   User AccessKeySecret
```

## Manage tables

You can use Tablestore CLI to perform the following operations on a table:

-   Create a table

    ```
    Sample:
        # Create a table that contains the first primary key of string type and the second primary key of integer type.
        # Specify the second primary key as an auto-increment column of integer type.
        # Set time to live and the maximum number of versions.
        create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
        create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer", "opt":"auto"}]'
        create -t mytable --pk '[{"C":"uid","t":"string"}, {"c":"pid","t":"integer"}]' --ttl 864000 --version 1
    Flags:
      -i, --input string           Create table json
      -p, --pk string              Primary Key
          --read_cu string         read capacity unit (default "0")
      -t, --table string           table name
          --ttl string             Time to live, unit is second, default(-1), means never timeout. (default "-1")
          --version string         Max version, default(1) (default "1")
          --write_cu string        write capacity unit (default "0")
    ```

-   Select a table for data operations

    ```
    Sample:
        use -t tablename
    Flags:
      -t, --table string   table name
    ```

-   List table names

    ```
    $./ts list
    List all table name from instance
    ```

-   Update the table

    ```
    Sample:
        alter -t mytable --ttl 86400 --version 1 --read_cu 0 --write_cu 0
    Flags:
          --read_cu string         read capacity unit
      -t, --table string           table name
          --ttl string             Time to live, unit is second
          --version string         Max version
          --write_cu string        write capacity unit
    ```

-   Query the description of the table

    ```
    Sample:
        desc -t tablename
    Flags:
      -o, --output string   Output file path
      -t, --table string    Table name
    ```

-   Delete the table

    ```
    Sample:
        drop -t tablename
    Flags:
      -t, --table string   table name
      -y, --yes            confirm yes
    ```


## Single-row operations

You can use Tablestore CLI to perform the following single-row operations:

-   Insert a row into a table

    ```
    Sample:
        #Insert a row into a table. The row contains two primary keys and two attribute columns. The first primary key is “86”, and the second primary key is 6771. The first attribute column is named as “name” and the attribute column value is “redchen”.
        # Select a check logic for inserting the row: 1. Ignore the check. 2. Allow inserting the row that contains the same primary key. 3. Allow inserting the row that does not contain the same primary key.
        # Allow inserting data with a specified timestamp.
        # Allow inserting the row that contains an auto-increment column as the primary key.
        # Read and insert data from a file.
        put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'
        put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'  --condition ignore/exist/not_exist
        put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china", "ts":15327798534}]'
        put --pk '["86", null]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'
        put -i /tmp/input
    Flags:
          --attr string        Attribute column value
          --condition string   input condition, ignore/exist/not_exist (default "ignore")
      -i, --input string       input file
          --pk string          PrimaryKey value
    ```

-   Read a row

    ```
    Sample:
        get --pk '["86",6771]'
    Flags:
      -o, --output string   Output file path
          --pk string       Input primary key column value
    ```

-   Update a row

    ```
    Sample:
        update --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'
        update --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'  --condition ignore/exist/not_exist
        update -i /tmp/input
    Flags:
          --attr string        Attribute column value
          --condition string   input condition, ignore/exist/not_exist (default "ignore")
      -i, --input string       input file
          --pk string          PrimaryKey value
    ```

-   Delete a row

    ```
    Sample:
        del --pk '["86", 6771]'
    Flags:
          --pk string   PrimaryKey value
    ```


## Simple stress testing

You can use Tablestore CLI to perform the following operations of simple stress testing:

-   Enable stress testing

    ```
    Sample:
            # Create a table. In the table, the first primary key is "Partition", and the second primary key is "Row number".
            # Use this table to insert 10 rows into Partition "redchen".
            create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
            use -t mytable
            press_input --part redchen --count 10
    Flags:
          --begin string   Begin index, default 0
          --count string   Input count , length of single row is 1KB
          --part string    Partition key, default 'redchen' (default "redchen")
    ```

-   Check the stress testing status

    ```
    Sample:
            create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
            use -t mytable
            press_check --part redchen --begin 0 --count 1000
    Flags:
          --begin string   Begin index, default 0
          --count string   Check count
          --part string    Partition key
      -y, --yes            show cost time
    ```


## Data backup

You can use Tablestore CLI to perform the following operations to back up data:

-   Export data from a table to a disk file

    ```
    Sample:
        # Export all data from the current table to a disk file.
        # Export data from the specified column.
        export -o /tmp/mydata
        export -o /tmp/mydata -c attr1,attr2,attr3
    Flags:
      -c, --columns string   Column names
      -o, --output string    Output file path
    ```

-   Import data from a disk file to a table

    ```
    Sample:
        # Read data from a disk file and export the data to a table.
        # Ignore the timestamp check and use the current time.
        import -i /tmp/mydata
        import -i /tmp/mydata --ignore_ts
        '''
        file format:
        {"PK":{"Values":["redchen",0]},"Attr":{"Values":[{"C":"country","V":"china0"},{"C":"name","V":"redchen0"}]}}
        {"PK":{"Values":["redchen",1]},"Attr":{"Values":[{"C":"country","V":"china1"},{"C":"name","V":"redchen1"}]}}
        '''
    Flags:
          --ignore_ts      ignore timestamp
      -i, --input string   Input file name
    ```


