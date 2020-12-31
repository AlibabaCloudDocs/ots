# Use UDFs to process data

If your data stored in Tablestore is uniquely structured and you want to define development logic to process each row of data, such as parsing a specific JSON string, you can use User Defined Function \(UDF\).

## Procedure

1.  Install the MaxCompute-Java/MaxCompute-Studio plug-in in IntelliJ. For more information, see [What is Studio](https://www.alibabacloud.com/help/doc-detail/50889.htm). After the plug-in is installed, you can start development.

    The following figure shows a simple UDF definition, which connects two strings. MaxCompute supports more complex UDF, such as user-defined window execution logic. For more information, see [Develop and debug UDF](https://www.alibabacloud.com/help/doc-detail/50902.htm).

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9645309951/p12003.png)

2.  Install the MaxCompute-Java/MaxCompute-Studio plug-in in IntelliJ. For more information, see [What is Studio](https://partners-intl.aliyun.com/help/doc-detail/50889.htm). After the plug-in is installed, you can start development.

    The following figure shows a simple UDF definition, which connects two strings. MaxCompute supports more complex UDF, such as user-defined window execution logic. For more information, see [Develop and debug UDF](https://partners-intl.aliyun.com/help/doc-detail/50902.htm).

3.  Upload the package to MaxCompute after the resource is packaged.

    Choose **File** \> **Project Structure** \> **Artifacts**. Enter the name and output directory. Then, click the **+** icon to select the output module. After the resource is packaged, use MaxCompute Project Explorer to upload the resource and create a function. Then, you can call the function in SQL.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/9645309951/p12004.png)

4.  Run bin/odpscmd.bat.

    ```
    // Select a row of data and pass in name/name to the UDF. A connection of the two strings is returned.
    select cloud_metric_extract_md5(name, name) as udf_test from test_table limit 1;                    
    ```

    A similar output is returned.

    ![](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/0745309951/p12005.png)


