# TimeRange

查询数据时指定的时间戳范围或特定时间戳值。

## 数据结构

```language-protobuf
message TimeRange {
    optional int64 start_time = 1;
    optional int64 end_time = 2;
    optional int64 specific_time = 3;
}
			
```

start\_time:

-   类型：int64

-   描述：起始时间戳。单位是毫秒。时间戳的取值最小值为0，最大值为INT64.MAX。


end\_time:

-   类型：int64

-   描述：结束时间戳。单位是毫秒。时间戳的取值最小值为0，最大值为INT64.MAX。


specific\_time:

-   类型：int64

-   描述：特定的时间戳值。specific\_time和\[start\_time, end\_time\) 两个中设置一个即可。单位是毫秒。时间戳的取值最小值为0，最大值为INT64.MAX。

**说明：** \[start\_time, end\_time\) 表示大于等于start\_time，小于end\_time。


