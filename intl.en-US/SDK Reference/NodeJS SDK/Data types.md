# Data types

Tablestore supports five data types. This topic describes how each of these data types corresponds to those supported in Tablestore SDK for Node.js.

**Note:** For more information about the data types supported by Tablestore, see [Naming conventions and data types](/intl.en-US/Developer Guide/Wide Column model/Naming conventions and data types.md).

|Data type in Tablestore|Data type in SDK for Node.js|Description|
|:----------------------|:---------------------------|:----------|
|String|string|The basic data type in JavaScript.|
|Integer|int64|The type of data encapsulated by SDK for Node.js.|
|Double|number|The basic data type in JavaScript.|
|Boolean|boolean|The basic data type in JavaScript.|
|Binary|Buffer|The Buffer object of Node.js.|

The Integer data type in Tablestore is 64-bit signed Integer and does not have a corresponding data type in JavaScript. A data type that supports 64-bit signed integer is required for Node.js. The following code provides an example on how to convert the Integer data type in Tablestore:

```
var numberA = TableStore.Long.fromNumber(1000);
var numberB = TableStore.Long.fromString('2000');

var num = numberA.toNumber();
    num = numberA.toString();

var str = numberB.toNumber();
    str = numberB.toString();
        
```

