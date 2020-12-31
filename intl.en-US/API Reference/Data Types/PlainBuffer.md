# PlainBuffer

Compared with Protocol Buffer, PlainBuffer delivers better performance for serialization and resolution of small objects. Therefore, PlainBuffer is used to format data in Tablestore.

## Format definition

```
plainbuffer = tag_header row1  [row2]  [row3]
row = ( pk [attr] | [pk] attr | pk attr ) [tag_delete_marker] row_checksum;
pk = tag_pk cell_1 [cell_2] [cell_3]
attr  = tag_attr cell1 [cell_2] [cell_3]
cell = tag_cell cell_name [cell_value] [cell_op] [cell_ts] cell_checksum
cell_name = tag_cell_name  formated_value
cell_value = tag_cell_value formated_value
cell_op = tag_cell_op  cell_op_value
cell_ts = tag_cell_ts cell_ts_value
row_checksum = tag_row_checksum row_crc8
cell_checksum = tag_cell_checksum row_crc8

formated_value = value_type value_len value_data
value_type = int8
value_len = int32

cell_op_value = delete_all_version | delete_one_version
cell_ts_value = int64 
delete_all_version = 0x01 (1byte)
delete_one_version = 0x03 (1byte)
                
```

## Tag values

```
tag_header = 0x75 (4byte)
tag_pk = 0x01 (1byte)
tag_attr = 0x02 (1byte)
tag_cell = 0x03 (1byte)
tag_cell_name = 0x04 (1byte)
tag_cell_value = 0x05 (1byte)
tag_cell_op = 0x06 (1byte)
tag_cell_ts = 0x07 (1byte)
tag_delete_marker = 0x08 (1byte)
tag_row_checksum = 0x09 (1byte)
tag_cell_checksum = 0x0A (1byte)
            
```

## ValueType values

Valid values of value\_type in formated\_value:

```
VT_INTEGER = 0x0
VT_DOUBLE = 0x1
VT_BOOLEAN = 0x2
VT_STRING = 0x3
VT_NULL = 0x6
VT_BLOB = 0x7
VT_INF_MIN = 0x9
VT_INF_MAX = 0xa
VT_AUTO_INCREMENT = 0xb
            
```

## Checksum calculation

The checksum is calculated based on the following basic logic:

-   The name, value, type, and timestamp of each cell is calculated.
-   The delete mark of each row is calculated. A byte of 1 is added for each row that has a delete mark. A byte of 0 is added for each row that does not have a delete mark.
-   After the checksum is calculated for each cell in a row, these cell checksums are used to calculate the row checksum. The CRC is performed only on the cells in the row, instead of data in the row.

Java implementation:

**Note:** The following code is extracted from `$tablestore-4.2.1-sources/com/alicloud/openservices/tablestore/core/protocol/PlainBufferCrc8.java`. For more information, see [Installation](/intl.en-US/SDK Reference/Java SDK/Installation.md).

```language-c++
public static byte getChecksum(byte crc, PlainBufferCell cell) throws IOException {

    if (cell.hasCellName()) {
        crc = crc8(crc, cell.getNameRawData());
    }

    if (cell.hasCellValue()) {
        if (cell.isPk()) {
            crc = cell.getPkCellValue().getChecksum(crc);
        } else {
            crc = cell.getCellValue().getChecksum(crc);
        }
    }

    if (cell.hasCellTimestamp()) {
        crc = crc8(crc, cell.getCellTimestamp());
    }

    if (cell.hasCellType()) {
        crc = crc8(crc, cell.getCellType());
    }

    return crc;
}

public static byte getChecksum(byte crc, PlainBufferRow row) throws IOException {
    for (PlainBufferCell cell : row.getPrimaryKey()) {
        crc = crc8(crc, cell.getChecksum());
    }

    for (PlainBufferCell cell : row.getCells()) {
        crc = crc8(crc, cell.getChecksum());
    }

    byte del = 0;
    if (row.hasDeleteMarker()) {
        del = (byte)0x1;
    }
    crc = crc8(crc, del);

    return crc;
}
            
```

## Example

A row contains two primary key columns and four attribute columns. The data types of the primary key columns are string and integer, while the data types in the attribute columns are string, integer, and double. The versions are 1001, 1002, and 1003. The fourth attribute column indicates whether to delete all versions.

-   Primary key columns:
    -   \[pk1:string:iampk\]
    -   \[pk2:integer:100\]
-   Attribute columns:
    -   \[column1:string:bad:1001\]
    -   \[column2:integer:128:1002\]
    -   \[column3:double:34.2:1003\]
    -   \[column4:del\_all\_versions\]

Encoding:

```
<The start position for headers>[0x75]
<The start position for primary key columns>[0x1]
  <Cell1>[0x3][0x4][0x3][3][pk1][0x5][0x3][5][iampk][_cell_checksum]
  <Cell2>[0x3][0x4][0x3][3][pk2][0x5][0x0][8][100][_cell_checksum]
<The start position for attribute columns>[0x2]
  <Cell1>[0x3][0x4][0x3][7][column1][0x5][0x3][3][bad][0x7][1001][_cell_checksum]
  <Cell2>[0x3][0x4][0x3][7][column2][0x5][0x0][8][128][0x7][1002][_cell_checksum]
  <Cell3>[0x3][0x4][0x3][7][column3][0x5][0x1][8][34.2][0x7][1003][_cell_checksum]
  <Cell4>[0x3][0x4][0x3][7][column4][0x6][1][_cell_checksum]
[_row_check_sum]
```

