# Endpoint

Each Tablestore instance has an endpoint in each type of network. You must specify an endpoint before any operations can be performed on data in Tablestore. Different instance endpoints are used in different scenarios.

**Note:** For more information about region names, see [Region](/intl.en-US/Function Introduction/Terms/Region.md).

## Query endpoints

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).
2.  On the Overview page, click the name of an instance or click **Manage Instance** in the Actions column corresponding to the instance.

    Go to the Instance Details tab. You can view the endpoints of the instance in the Instance Access URL section.


## Public endpoint

Public endpoints are used to access Tablestore instances over the Internet.

Format:

`https://instanceName.region.ots.aliyuncs.com`

For example, the public endpoint used to access an instance named myInstance that is located in the China \(Hangzhou\) region is:

`https://myInstance.cn-hangzhou.ots.aliyuncs.com`

## VPC endpoint

VPC endpoints are used to access Tablestore instances from ECS instances in VPCs.

Format:

`https://instanceName.region.vpc.tablestore.aliyuncs.com`

For example, the VPC endpoint used to access an instance named myInstance that is located in the China \(Hangzhou\) region from VPCs is:

`https://myInstance.cn-hangzhou.vpc.tablestore.aliyuncs.com`

## Private endpoint

Private endpoints are used to access Tablestore instances from ECS instances in the classic network in the same region. When applications access Tablestore instances from ECS instances in the classic network in the same region over the internal network, the response latency is reduced and no Internet traffic is generated.

Format:

`https://instanceName.region.ots-internal.aliyuncs.com`

For example, the private endpoint used to access an instance named myInstance that is located in the China \(Hangzhou\) region is:

`https://myInstance.cn-hangzhou.ots-internal.aliyuncs.com`

