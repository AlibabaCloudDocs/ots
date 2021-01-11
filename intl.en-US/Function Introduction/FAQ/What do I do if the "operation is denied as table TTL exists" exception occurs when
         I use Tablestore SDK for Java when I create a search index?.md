# What do I do if the "operation is denied as table TTL exists" exception occurs when I use Tablestore SDK for Java when I create a search index?

This topic describes the problem description, cause, and solution of the "operation is denied as table TTL exists" exception when you create a search index.

## Problem description

The following error occurs when you create a search index:

```
ErrorCode: OTSParameterInvalid, ErrorMessage: operation is denied as table TTL exists.
```

## Cause

The time to live \(TTL\) of a table is set to a value other than -1\(The data never expires\). You can create a search index only when the TTL of a table is set to -1.

## Solution

Set the TTL of a table to -1 by using the console or SDKs.

-   Use the console

    Log on to the Tablestore console. Go to the Details tab. Click **Modify Attributes**. In the dialog box, set the TTL to -1, and click **OK**.

-   Use SDKs

    Call the UpdateTable operation to set the TTL of a table to -1 based on corresponding SDKs.

    -   Tablestore SDK for Java: [Update a table](/intl.en-US/SDK Reference/Java SDK/Table/Update a table.md)
    -   Tablestore SDK for Go: [Update a table](/intl.en-US/SDK Reference/Go SDK/Table/Update a table.md)
    -   Tablestore SDK for Python: [Update a table](/intl.en-US/SDK Reference/Python SDK/Table/Update a table.md)
    -   Tablestore SDK for Node.js: [Update a table](/intl.en-US/SDK Reference/Node.js SDK/Table/Update a table.md)
    -   Tablestore SDK for .NET: [Update a table](/intl.en-US/SDK Reference/.NET SDK/Table/Update a table.md)
    -   Tablestore SDK for PHP: [Update a table](/intl.en-US/SDK Reference/PHP SDK/Table operations/Update a table.md)

