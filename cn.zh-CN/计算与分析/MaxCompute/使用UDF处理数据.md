# 使用UDF处理数据

如果您在表格存储里面的数据有着独特的结构，希望自定义开发逻辑来处理每一行数据，例如解析特定的JSON字符串，可以使用UDF（User Defined Function，即用户自定义函数）来处理。

## 操作步骤

1.  参考[MaxCompute Studio](https://help.aliyun.com/document_detail/50889.html)文档，在IntelliJ中安装MaxCompute-Java/MaxCompute-Studio插件。插件安装完毕，即可直接开发。

    下图是一个简单的UDF定义，将两个字符串连接。MaxCompute支持更复杂的UDF，包括自定义窗口执行逻辑等。更多信息，请参见[开发和调试UDF](https://help.aliyun.com/document_detail/50902.html)。

    ![fig_udf_001](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9105397751/p12003.png)

2.  参考[MaxCompute Studio](https://partners-intl.aliyun.com/help/doc-detail/50889.htm)文档，在IntelliJ中安装MaxCompute-Java/MaxCompute-Studio插件。插件安装完毕，即可直接开发。

    下图是一个简单的UDF定义，将两个字符串连接。MaxCompute支持更复杂的UDF，包括自定义窗口执行逻辑等。更多信息，请参见[开发和调试UDF](https://partners-intl.aliyun.com/help/doc-detail/50902.htm)。

3.  包之后可以上传到MaxCompute。

    选择**File** \> **Project Structure** \> **Artifacts**，输入Name和Output directory后，单击**+**选择输出模块。打包后通过ODPS Project Explorer来上传资源、创建函数，然后就可以在SQL中调用。

    ![fig_udf_003](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9105397751/p12004.png)

4.  运行bin/odpscmd.bat。

    ```
    // 选出来1行数据，并将name/name传入UDF，返回两个string的累加.
    select cloud_metric_extract_md5(name, name) as udf_test from test_table limit 1;                    
    ```

    返回结果如下：

    ![fig_udf_004](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/9105397751/p12005.png)


