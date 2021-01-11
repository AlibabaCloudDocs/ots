# What do I do if the length of field value is longer than 10 for the \[WILDCARD\_QUERY\] query exception occurs when I query data by using wildcard query?

This topic describes the problem description, cause, and solution of the length of field value is longer than 10 for the \[WILDCARD\_QUERY\] query exception when you query data by using wildcard query.

## Problem description

The following exception occurs when you query data by using wildcard query:

```
length of field value is longer than 10 for the [WILDCARD_QUERY] query
```

## Cause

A string that contains wildcards is greater than 10 bytes in length.

## Solution

When you query data by using wildcard query, the value to be matched can be a string that contains wildcards smaller than or equal to 10 bytes in length.

