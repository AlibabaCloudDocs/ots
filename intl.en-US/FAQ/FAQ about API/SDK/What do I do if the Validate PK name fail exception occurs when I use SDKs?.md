# What do I do if the Validate PK name fail exception occurs when I use SDKs?

This topic describes the problem, cause, and solution of the Validate PK name fail exception that may occur when you use SDKs.

## Problem description

The following exception occurs when you use SDKs to query data:

```
Caused by: [ErrorCode]:OTSInvalidPK, [Message]:Validate PK name fail
```

## Cause

The specified names of primary key columns are different from those in the data table, or the sequence of primary key columns is different from that in the data table.

## Solution

The specified names and sequence of the primary key columns must be the same as those of the table.

