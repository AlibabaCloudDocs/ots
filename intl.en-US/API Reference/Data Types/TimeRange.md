# TimeRange

TimeRange specifies the timestamp range or timestamp value to query.

## Data structure

```language-protobuf
message TimeRange {
    optional int64 start_time = 1;
    optional int64 end_time = 2;
    optional int64 specific_time = 3;
}
			
```

start\_time:

-   Type: int64

-   Description: the starting timestamp. Unit: milliseconds. Valid values: \[0, INT64.MAX\).


end\_time:

-   Type: int64

-   Description: the ending timestamp. Unit: milliseconds. Valid values: \[0, INT64.MAX\).


specific\_time:

-   Type: int64

-   Description: the specific timestamp. You can set one of specific\_time and \[start\_time, end\_time\). Unit: milliseconds. Valid values: \[0, INT64.MAX\).

**Note:** \[start\_time, end\_time\) indicates a time range that starts from the start timestamp and ends at the end timestamp. The range includes the start timestamp and excludes the end timestamp.


