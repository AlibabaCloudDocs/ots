# Billing overview

Tablestore charges resources for each instance. This topic describes the pricing, billing methods, and billing items of Tablestore.

## Pricing

For more information about pricing, see [Tablestore Pricing](https://www.alibabacloud.com/product/table-store/pricing).

## Billing methods

Tablestore charges resources based on the pay-as-you-go billing method. This method indicates that Tablestore charges resources you have used on an hourly basis.

## Billing items

The following table lists the billing items of Tablestore.

|Billing item|Billing method|
|------------|--------------|
|[Storage usage](/intl.en-US/Pricing/Storage usage.md)|The total storage usage charged based on the average storage usage per hour in a billing cycle. |
|[Read throughput](/intl.en-US/Function Introduction/Terms/Read/write throughput.md)|Reserved read throughput|The total reserved read CUs of all tables charged based on the average reserved read CUs per hour in a billing cycle.

**Note:** Reserved read CUs are available only for high-performance instances. For more information about instances, see [Instance](/intl.en-US/Function Introduction/Terms/Instance.md). |
|Additional read throughput|The total additional read CUs of all tables charged based on the average additional read CUs per second in a billing cycle. |
|[Write throughput](/intl.en-US/Function Introduction/Terms/Read/write throughput.md)|Reserved write throughput|The total reserved write CUs of all tables charged based on the average reserved write CUs per hour in a billing cycle.

**Note:** Reserved write CUs are available only for high-performance instances. For more information about instances, see [Instance](/intl.en-US/Function Introduction/Terms/Instance.md). |
|Additional write throughput|The total additional write CUs of all tables charged based on the average additional write CUs per second in a billing cycle. |
|Internet outbound traffic|The total Internet outbound traffic generated when applications access Tablestore over HTTP.

**Note:**

-   Tablestore charges Internet outbound traffic instead of inbound traffic and internal network traffic.
-   When access fails, Tablestore returns access failure information, which also generates outbound traffic.
-   Access between different regions is also billed as Internet access. |

**Note:**

-   You are also charged when you use search indexes. For more information about the billing, see [Billable items of search indexes](/intl.en-US/Pricing/Billable items of search indexes.md).
-   You are also charged when you use secondary indexes. For more information about the billing, see [Billing rules](/intl.en-US/Pricing/Billable items of global secondary indexes.md).

