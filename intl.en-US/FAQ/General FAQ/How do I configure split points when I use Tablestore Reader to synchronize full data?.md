# How do I configure split points when I use Tablestore Reader to synchronize full data?

This topic describes the causes and solutions of slow synchronization when Tablestore Reader is used to synchronize full data.

## Problem description

Synchronization is slow when Tablestore Reader is used to synchronize full data. The following example shows a script used to configure full data synchronization:

```
"reader": {
  "plugin": "ots",
  "parameter": {
    "datasource": "",
    "table": "",
    "column": [],
    "range": {
      "begin": [
        {
          "type": "INF_MIN"
        }
      ],
      "end": [
        {
          "type": "INF_MAX"
        }
      ]
    }
  }
}
```

## Cause

The amount of the data to synchronize is large and split points are not configured in the script. If only one thread is create to obtain the data in the synchronization task, the synchronization is slow.

## Solution

When you use Tablestore Reader to synchronize a large amount of data, configure split points in the script. To configure split points in the script, perform the following steps:

1.  Obtain the information about the required split points in one of the following methods:

    -   Use Tablestore SDK to call the [ComputeSplitPointsBySize](/intl.en-US/API Reference/Operations/ComputeSplitPointsBySize.md) operation. For more information, see [Split data by a specified size](/intl.en-US/SDK Reference/Java SDK/Table/Split data by a specified size.md).
    -   Download the Tablestore CLI tool and run the following command: points -s splitSize -t tablename. For more information, see [Tablestore CLI](/intl.en-US/Utilities/Tablestore CLI.md).
    **Note:** The unit of the splitSize value is 100 MB. You do not need to configure split points when the amount of data to synchronize is small. However, when the amount of data is large, we recommend that you configure the splitSize value to support the maximum number of concurrent threads supported by your environment.

2.  Configure split points in the script used for synchronization. The following script provides an example on how to configure split points:

    ```
    "range": {
          "begin": [
            {
              "type": "INF_MIN"
            }
          ],
          "end": [
            {
              "type": "INF_MAX"
            }
          ],
          "split": [
            {
              "type": "STRING",
              "value": "splitPoint1"
            },
            {
              "type": "STRING",
              "value": "splitPoint2"
            },
            {
              "type": "STRING",
              "value": "splitPoint3"
            }
          ]
    }
    ```


If the synchronization remains slow after you configure split points, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) to contact the technical support.

