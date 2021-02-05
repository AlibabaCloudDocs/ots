# Preparations

This topic describes how to make environment preparations to access a Tablestore table by using Hive and HadoopMR.

## Use Hive and HadoopMR to access a Tablestore table

You can use Hive and HadoopMR to access and analyze data in Tablestore directly by using the dependency package released by the official teams of [Tablestore](https://www.alibabacloud.com/product/table-store) and [E-MapReduce](https://www.alibabacloud.com/product/e-mapreduce).

## Install JDK V7 or later

1.  Download the installation package of JDK V7 or later. Install JDK V7 or later.

    -   Linux or MacOS: Use the package manager built in the system.

    -   Windows: For more information about the download path, visit [Java SE Development Kit 8 Downloads](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

2.  Follow the example to check the installation.

    ```
        $ java -version
        java version "1.8.0_77"
        Java(TM) SE Runtime Environment (build 1.8.0_77-b03)
        Java HotSpot(TM) 64-Bit Server VM (build 25.77-b03, mixed mode)
                            
    ```


## Install Hadoop and start the Hadoop environment

1.  Download the Hadoop installation package whose version is later than 2.6.0. For more information, visit [Index of /apache/hadoop/common](http://mirrors.cnnic.cn/apache/hadoop/common/).

2.  Decompress the package and install Hadoop based on the cluster conditions.

3.  Follow the example to start the Hadoop environment.

    ```
    $ bin/start-all.sh
    # Check whether the service is started.
    $ jps
    24017 NameNode
    24835 Jps
    24131 DataNode
    24438 ResourceManager
    5114 HMaster
    24287 SecondaryNameNode
    24527 NodeManager
                            
    ```

4.  Add the path of Hadoop in /etc/profile. Run the source /etc/profile command to make the configurations take effect.

    ```
    export HADOOP_HOME=/data/hadoop/hadoop-2.6.0
    export PATH=$PATH:$HADOOP_HOME/bin                  
    ```


## Download the Hive installation package and install Hive

1.  Download the Hive installation package of the bin.tar.gz type. For more information about the download path, visit [DOWNLOADS](https://hive.apache.org/downloads.html).

2.  Follow the example to decompress the installation package.

    ```
    $ mkdir /home/admin/hive-2.1.0
    $ tar -zxvf apache-hive-2.1.0-bin.tar.gz -C /home/admin/
    $ mv /home/admin/apache-hive-2.1.0-bin /home/admin/hive-2.1.0/
                            
    ```

3.  Follow the example to initialize schema.

    ```
        # Go to the specified directory.
        $ cd /home/admin/hive-2.1.0/
    
        # Start initialization. For mysql, replace derby with mysql.
        # If an error occurs in the execution process, delete rm -rf metastore_db/ before you execute the code again.
        $ ./bin/schematool -initSchema -dbType derby
                            
    ```

4.  Follow the example to start the Hive environment.

    ```
    $ ./bin/hive
    # Check whether the service is started.
    hive> show databases;
    OK
    default
    Time taken: 0.207 seconds, Fetched: 1 row(s)                
    ```


## Download Tablestore SDK for Java

1.  Download related dependent packages of Java SDK whose version is later than 4.1.0 from the Maven library. For more information about the download path, see [Tablestore SDK for Java](/intl.en-US/SDK Reference/Download SDK/Tablestore SDK for Java.md).

    Related dependent packages of Java SDK are released with the latest Java SDK. Download the latest related dependent packages.

2.  Follow the example to copy the SDK to the directory of Hive.

    ```
    $ mv tablestore-4.1.0-jar-with-dependencies.jar /home/admin/hive-2.1.0/                  
    ```


## Download Alibaba Cloud EMR SDK

Download the dependent packages of EMR SDK. For more information about the specific download path, visit [aliyun-emapreduce-datasources](https://github.com/aliyun/aliyun-emapreduce-sdk).

