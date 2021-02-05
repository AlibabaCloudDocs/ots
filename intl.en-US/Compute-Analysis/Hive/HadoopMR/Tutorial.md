# Tutorial

This topic describes how to use Hive and HadoopMR to access a Tablestore table.

## Data preparation

Prepare a Tablestore table named pet. The name column is the only primary key column. The following table lists the data in the table.

**Note:** You do not need to write data to the empty fields. Tablestore is schema-free. You do not need to write NULL even if no values exist.

|name|owner|species|sex|birth|death|
|:---|:----|:------|:--|-----|-----|
|Fluffy|Harold|cat|f|1993-02-04| |
|Claws|Gwen|cat|m|1994-03-17| |
|Buffy|Harold|dog|f|1989-05-13| |
|Fang|Benny|dog|m|1990-08-27| |
|Bowser|Diane|dog|m|1979-08-31|1995-07-29|
|Chirpy|Gwen|bird|f|1998-09-11| |
|Whistler|Gwen|bird| |1997-12-09| |
|Slim|Benny|snake|m|1996-04-29| |
|Puffball|Diane|hamster|f|1999-03-30| |

## Examples of access by using Hive

Prerequisites

Examples

```
# Add HADOOP_HOME and HADOOP_CLASSPATH to /etc/profile.
$ export HADOOP_HOME=${The installation directory of your Hadoop}
$ export HADOOP_CLASSPATH=emr-tablestore-1.4.2.jar:tablestore-4.3.1-jar-with-dependencies.jar:joda-time-2.9.4.jar
$ bin/hive
hive> CREATE EXTERNAL TABLE pet
  (name STRING, owner STRING, species STRING, sex STRING, birth STRING, death STRING)
  STORED BY 'com.aliyun.openservices.tablestore.hive.TableStoreStorageHandler'
  WITH SERDEPROPERTIES(
    "tablestore.columns.mapping"="name,owner,species,sex,birth,death")
  TBLPROPERTIES (
    "tablestore.endpoint"="YourEndpoint",
    "tablestore.access_key_id"="YourAccessKeyId",
    "tablestore.access_key_secret"="YourAccessKeySecret",
    "tablestore.table.name"="pet");
hive> SELECT * FROM pet;
Bowser  Diane   dog     m       1979-08-31      1995-07-29
Buffy   Harold  dog     f       1989-05-13      NULL
Chirpy  Gwen    bird    f       1998-09-11      NULL
Claws   Gwen    cat     m       1994-03-17      NULL
Fang    Benny   dog     m       1990-08-27      NULL
Fluffy  Harold  cat     f       1993-02-04      NULL
Puffball        Diane   hamster f       1999-03-30      NULL
Slim    Benny   snake   m       1996-04-29      NULL
Whistler        Gwen    bird    NULL    1997-12-09      NULL
Time taken: 5.045 seconds, Fetched 9 row(s)
hive> SELECT * FROM pet WHERE birth > "1995-01-01";
Chirpy  Gwen    bird    f       1998-09-11      NULL
Puffball        Diane   hamster f       1999-03-30      NULL
Slim    Benny   snake   m       1996-04-29      NULL
Whistler        Gwen    bird    NULL    1997-12-09      NULL
Time taken: 1.41 seconds, Fetched 4 row(s)
            
```

Parameter descriptions:

-   WITH SERDEPROPERTIES

    tablestore.columns.mapping: optional. By default, the field names of an external table are the column \(primary key column or attribute column\) names of the corresponding Tablestore table. However, some scenarios where the field names of an external table are inconsistent with the column names of a Tablestore table exist. For example, issues related to case sensitivity or character sets exist. In these scenarios, specify tablestore.columns.mapping. This parameter is a string separated by a comma \(,\). No spaces are allowed at both ends of the comma \(,\). Each item indicates the column name of the table. The order of the column names is consistent with that of the field names of the corresponding external table.

    **Note:** The name of a column in Tablestore can be an empty string. Therefore, the empty string is considered to be part of the name of a column in a table.

-   TBLPROPERTIES
    -   tablestore.endpoint: required. The [endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md) used to access Tablestore. You can view the endpoint information of an instance in the Tablestore console.

    -   tablestore.instance: optional. The name of the [instance](/intl.en-US/Function Introduction/Terms/Instance.md) in Tablestore. If this parameter is not specified, the instance name is the first field of tablestore.endpoint.

    -   tablestore.table.name: required. The name of the table in Tablestore.

    -   tablestore.access\_key\_id and tablestore.access\_key\_secret: required. For more information, see [Obtain an AccessKey pair]().

    -   tablestore.sts\_token: optional. For more information, see [Configure user permissions](/intl.en-US/Function Introduction/Authorization management/Configure user permissions.md).


## Example of access by using HadoopMR

The following examples provide an example on how to use HadoopMR to collect statistics for the count of rows in the pet table.

Examples

-   Construct Mappers and Reducers

    ```
    public class RowCounter {
    public static class RowCounterMapper
    extends Mapper<PrimaryKeyWritable, RowWritable, Text, LongWritable> {
        private final static Text agg = new Text("TOTAL");
        private final static LongWritable one = new LongWritable(1);
    
        @Override
        public void map(
            PrimaryKeyWritable key, RowWritable value, Context context)
            throws IOException, InterruptedException {
            context.write(agg, one);
        }
    }
    
    public static class IntSumReducer
    extends Reducer<Text,LongWritable,Text,LongWritable> {
    
        @Override
        public void reduce(
            Text key, Iterable<LongWritable> values, Context context)
            throws IOException, InterruptedException {
            long sum = 0;
            for (LongWritable val : values) {
                sum += val.get();
            }
            context.write(key, new LongWritable(sum));
        }
    }
    }
                        
    ```

    map\(\) of mapper is called each time the data source reads a row of data from Tablestore. The PrimaryKeyWritable parameter specifies the primary key of the row. The RowWritable parameter specifies the content of the row. You can call PrimaryKeyWritable.getPrimaryKey\(\) to obtain the primary key object defined in Tablestore SDK for Java and RowWritable.getRow\(\) to obain the row object defined in the SDK.

-   Configure Tablestore to use it as the data source of mapper.

    ```
    private static RangeRowQueryCriteria fetchCriteria() {
        RangeRowQueryCriteria res = new     RangeRowQueryCriteria("YourTableName");
        res.setMaxVersions(1);
        List<PrimaryKeyColumn> lower = new ArrayList<PrimaryKeyColumn>();
        List<PrimaryKeyColumn> upper = new ArrayList<PrimaryKeyColumn>();
        lower.add(new PrimaryKeyColumn("YourPkeyName", PrimaryKeyValue.INF_MIN));
        upper.add(new PrimaryKeyColumn("YourPkeyName", PrimaryKeyValue.INF_MAX));
        res.setInclusiveStartPrimaryKey(new PrimaryKey(lower));
        res.setExclusiveEndPrimaryKey(new PrimaryKey(upper));
        return res;
    }
    
    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "row count");
        job.addFileToClassPath(new Path("hadoop-connector.jar"));
        job.setJarByClass(RowCounter.class);
        job.setMapperClass(RowCounterMapper.class);
        job.setCombinerClass(IntSumReducer.class);
        job.setReducerClass(IntSumReducer.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(LongWritable.class);
        job.setInputFormatClass(TableStoreInputFormat.class);
        TableStoreInputFormat.setEndpoint(job, "https://YourInstance.Region.ots.aliyuncs.com/");
        TableStoreInputFormat.setCredential(job, "YourAccessKeyId", "YourAccessKeySecret");
        TableStoreInputFormat.addCriteria(job, fetchCriteria());
        FileOutputFormat.setOutputPath(job, new Path("output"));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }                    
    ```

    In the example, job.setInputFormatClass\(TableStoreInputFormat.class\) is used to set Tablestore to the data source. In addition, you must perform the following operations:

    -   Deploy hadoop-connector.jar to the cluster and add the path of hadoop-connector.jar to classpath. The path is the local path of hadoop-connector.jar. The path is specified in addFileToClassPath\(\). The code assumes that hadoop-connector.jar is in the current path.
    -   To access Tablestore, specify the entry and identity. Use TableStoreInputFormat.setEndpoint\(\) to set the endpoint and TableStoreInputFormat.setCredential\(\) to set the AccessKey pair information.
    -   Specify a table for counting.

        **Note:**

        -   A RangeRowQueryCriteria object defined in Java SDK is added to the data source each time addCriteria\(\) is called. addCriteria\(\) can be called multiple times. The limits are the same between the RangeRowQueryCriteria object and the RangeRowQueryCriteria object used in the GetRange operation of Tablestore SDK for Java.
        -   You can use setFilter\(\) and addColumnsToGet\(\) of RangeRowQueryCriteria to filter unnecessary rows and columns on the Tablestore server side. This way, the volume of data to be accessed and the cost are reduced while performance is improved.
        -   You can add multiple RangeRowQueryCriteria to multiple tables to implement union on multiple tables.
        -   Add multiple RangeRowQueryCriteria to a single table to improve splitting. TableStore-Hadoop Connector can split the range of the input of users based on some policies.

## Example of running the program

```
$ HADOOP_CLASSPATH=hadoop-connector.jar bin/hadoop jar row-counter.jar
...
$ find output -type f
output/_SUCCESS
output/part-r-00000
output/. _SUCCESS.crc
output/.part-r-00000.crc
$ cat out/part-r-00000
TOTAL   9           
```

## Data type conversions

The supported data types are different between Tablestore and Hive or Spark.

The following table lists the data type conversion from Tablestore \(rows\) to Hive or Spark \(columns\).

|Data type conversion|TINYINT|SMALLINT|INT|BIGINT|FLOAT|DOUBLE|BOOLEAN|STRING|BINARY|
|:-------------------|:------|:-------|:--|:-----|:----|:-----|:------|:-----|:-----|
|INTEGER|Supported \(with loss of precision\)|Supported \(with loss of precision\)|Supported \(with loss of precision\)|Supported|Supported \(with loss of precision\)|Supported \(with loss of precision\)|Not supported|Not supported|Not supported|
|DOUBLE|Supported \(with loss of precision\)|Supported \(with loss of precision\)|Supported \(with loss of precision\)|Supported \(with loss of precision\)|Supported \(with loss of precision\)|Supported|Not supported|Not supported|Not supported|
|BOOLEAN|Not supported|Not supported|Not supported|Not supported|Not supported|Not supported|Supported|Not supported|Not supported|
|STRING|Not supported|Not supported|Not supported|Not supported|Not supported|Not supported|Not supported|Supported|Not supported|
|BINARY|Not supported|Not supported|Not supported|Not supported|Not supported|Not supported|Not supported|Not supported|Supported|

