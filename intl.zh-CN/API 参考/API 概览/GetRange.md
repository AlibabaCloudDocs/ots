# GetRange

您可以使用GetRange接口读取指定主键范围内的数据。

## 请求结构

```language-protobuf
message GetRangeRequest {
    required string table_name = 1;
    required Direction direction = 2;
    repeated string columns_to_get = 3;  // 不指定则读出所有的列
    optional TimeRange time_range = 4;
    optional int32 max_versions = 5;
    optional int32 limit = 6;
    required bytes inclusive_start_primary_key = 7; // Plainbuffer编码为二进制
    required bytes exclusive_end_primary_key = 8; // Plainbuffer编码为二进制
    optional bytes filter = 10;
    optional string start_column = 11;
    optional string end_column = 12;
}
```

|名称|类型|是否必选|描述|
|--|--|----|--|
|table\_name|string|是|要读取的数据所在的表名。 |
|direction|[Direction](/intl.zh-CN/API 参考/DataType/Direction.md)|是|本次查询的顺序。

-   若为正序，则 inclusive\_start\_primary 应小于 exclusive\_end\_primary，响应中各行按照主键由小到大的顺序进行排列。
-   若为逆序，则 inclusive\_start\_primary 应大于 exclusive\_end\_primary，响应中各行按照主键由大到小的顺序进行排列。 |
|columns\_to\_get|repeated string|否|-   需要返回的全部列的列名。若为空，则返回读取结果中每行的所有列。

-   如果给出了重复的列名，返回结果只会包含一次该列。

-   columns\_to\_get 中 string 的个数不应超过 128 个。 |
|time\_range|[TimeRange](/intl.zh-CN/API 参考/DataType/TimeRange.md)|和max\_versions只能存在一个|-   读取数据的版本时间戳范围。

-   时间戳的取值最小值为0，最大值为INT64.MAX。

-   若要查询一个范围，则指定start\_time和end\_time。

-   若要查询一个特定时间戳，则指定specific\_time。


 例子：如果指定的time\_range为\(100, 200\)，则返回的列数据的时间戳必须位于\[100, 200\)范围内，前闭后开区间。 |
|max\_versions|int32|和time\_range只能存在一个|读取数据时，返回的最多版本个数。

 例子：如果指定max\_versions为2，则每一列最多返回2个版本的数据。 |
|limit|int32|否|-   本次读取最多返回的行数，若查询到的行数超过此值，则通过响应中包含的断点记录本次读取到的位置，以便下一次读取。此值必须大于 0。

-   无论是否设置此值，表格存储最多返回行数为 5000 且总数据大小不超过 4 MB。 |
|inclusive\_start\_primary\_key|bytes|是|本次范围读取的起始主键，若该行存在，则响应中一定会包含此行。由Plainbuffer编码，详见[Plainbuffer](/intl.zh-CN/API 参考/DataType/PlainBuffer.md)编码。 |
|exclusive\_end\_primary\_key|bytes|是|-   本次范围读取的终止主键，无论该行是否存在，响应中都不会包含此行。由Plainbuffer编码，详见[Plainbuffer](/intl.zh-CN/API 参考/DataType/PlainBuffer.md)编码。

-   在 GetRange 中，inclusive\_start\_primary\_key 和 exclusive\_end\_primary\_key 中的 Column 的 type 可以使用本操作专用的两个类型 INF\_MIN 和 INF\_MAX。类型为 INF\_MIN 的 Column 小于其它 Column；类型为 INF\_MAX 的 Column 大于其它 Column。 |
|filter|bytes|否|-   过滤条件表达式。

-   [Filter](/intl.zh-CN/API 参考/DataType/Filter.md)经过protobuf序列化后的二进制数据。 |
|start\_column|string|否|-   指定读取时的起始列，主要用于宽行读。

-   返回的结果中包含当前起始列。

-   列的顺序按照列名的字典序排序。


 例子：如果一张表有"a"，"b"，"c"三列，读取时指定start\_column为“b”，则会从"b"列开始读，返回"b"，"c"两列。 |
|end\_column|string|否|-   指定读取时的结束列，主要用于宽行读。

-   返回的结果中不包含当前结束列。

-   列的顺序按照列名的字典序排序。


 例子：如果一张表有"a"，"b"，"c"三列，读取时指定end\_column为“b”，则读到"b"列时会结束，返回"a"列。 |

## 响应消息结构

```language-protobuf
message GetRangeResponse {
    required ConsumedCapacity consumed = 1;
    required bytes rows = 2; 
    optional bytes next_start_primary_key = 3; 
}
```

|名称|类型|描述|
|--|--|--|
|consumed|[ConsumedCapacity](/intl.zh-CN/API 参考/DataType/ConsumedCapacity.md)|本次操作[消耗的服务能力单元](#section_2ij_qj7_ulf)。 |
|rows|bytes|-   由Plainbuffer编码，详见[Plainbuffer](/intl.zh-CN/API 参考/DataType/PlainBuffer.md)编码。

-   读取到的所有数据，若请求中 direction 为 FORWARD，则所有行按照主键由小到大进行排序；若请求中 direction 为 BACKWARD，则所有行按照主键由大到小进行排序。

-   其中每行的 primary\_key\_columns 和 attribute\_columns 均只包含在 columns\_to\_get 中指定的列，其顺序不保证与 request 中的 columns\_to\_get 一致；primary\_key\_columns 的顺序亦不保证与建表时指定的顺序一致。

-   如果请求中指定的 columns\_to\_get 不含有任何主键列，那么其主键在查询范围内。但没有任何一个属性列在 columns\_to\_get 中的行将不会出现在响应消息里。 |
|next\_start\_primary\_key|bytes|-   本次 GetRange 操作的断点信息。由Plainbuffer编码，详见[Plainbuffer](/intl.zh-CN/API 参考/DataType/PlainBuffer.md)编码。

-   若为空，则本次 GetRange 的响应消息中已包含了请求范围内的所有数据。

-   若不为空，则表示本次 GetRange 的响应消息中只包含了 \[inclusive\_start\_primary\_key, next\_start\_primary\_key\) 间的数据，若需要剩下的数据，需要将 next\_start\_primary\_key 作为 inclusive\_start\_primary\_key，原始请求中的 exclusive\_end\_primary\_key 作为 exclusive\_end\_primary\_key 继续执行 GetRange 操作。

**说明：** 表格存储系统中限制了 GetRange 操作的响应消息中数据不超过 5000 行，大小不超过 4 MB。即使在 GetRange 请求中未设定 limit，在响应中仍可能出现 next\_start\_primary\_key。因此在使用 GetRange 时一定要对响应中是否有 next\_start\_primary\_key 进行处理。 |

## 服务能力单元消耗

-   GetRange 操作消耗读服务能力单元的数值为查询范围内所有行主键数据大小与实际读取的属性列数据大小之和除以 4 KB 向上取整。关于数据大小的计算请参见[产品定价](/intl.zh-CN/产品定价/计费概述.md)。

-   如果请求超时，结果未定义，服务能力单元有可能被消耗，也可能未被消耗。

-   如果返回内部错误（HTTP 状态码：5XX），则此次操作不消耗服务能力单元，其他错误情况消耗 1 个读服务能力单元。


