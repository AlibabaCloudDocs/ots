# Initialization

OTSClient is the client for Tablestore. OTSClient provides a series of methods for you to manage tables and perform read and write operations on one or more rows. To use Tablestore SDK for PHP to initiate a request, you must initialize an OTSClient instance and modify the default configuration items of OTSClientConfig.

## Obtain an endpoint

An endpoint is the domain name used to access a Tablestore instance in a region. The following table lists the examples of endpoints in the supported format.

|Example|Description|
|:------|:----------|
|http://sun.cn-hangzhou.ots.aliyuncs.com|The public endpoint used to access the sun instance in the China \(Hangzhou\) region over HTTP.|
|https://sun.cn-hangzhou.ots.aliyuncs.com|The public endpoint used to access the sun instance in the China \(Hangzhou\) region over HTTPS.|

**Note:** Tablestore supports access over the Internet and internal network. For more information, see [Endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md).

To query the endpoints of your Tablestore instance, perform the following operations:

1.  Log on to the Tablestore console.

2.  Click the instance name to go to the Instance Management page.

    On the Instance Details tab, you can view the endpoints of the instance in Instance Access URL.


## Configure an AccessKey pair

To access Tablestore, you must have an AccessKey pair that consists of an AccessKey ID and AccessKey secret to verify your identity. The following types of AccessKey pairs are supported:

-   The AccessKey pair of an Alibaba Cloud account. To obtain the AccessKey pair of an Alibaba Cloud account, take the following steps:
    1.  On the Alibaba Cloud official website,[Create Your Alibaba Cloud Account](https://account-intl.aliyun.com/register/intl_register.htm).
    2.  Create an AccessKey ID and an AccessKey secret on the [User Management](https://ak-console.aliyun.com/#/accesskey) console.
-   The AccessKey pair of a RAM user who is granted with permissions to access Tablestore. To obtain the AccessKey pair of a RAM user, take the following steps:
    1.  Use your Alibaba Cloud account to log on to [RAM](https://www.aliyun.com/product/ram/). Create a RAM user or use an existing RAM user.
    2.  Use your Alibaba Cloud account to authorize the RAM user to access Tablestore.
    3.  After the RAM user is authorized, you can use the AccessKey pair of the RAM user to access Tablestore.
-   Temporary access credentials obtained from STS. To obtain temporary access credentials from STS, take the following steps:
    1.  The application server uses RAM or STS to obtain access credentials that consist of a temporary AccessKey ID, an AccessKey secret, and a token. After the access credentials are obtained, the application server sends them to you.
    2.  You can use the received access credentials to access Tablestore.

## Initialize an OTSClient instance

After you obtain the AccessKey ID and AccessKey secret, you can perform the following operations to initialize an OTSClient instance:

1.  Use the endpoint of a Tablestore instance to create a client.

    Examples

    ```
        $otsClient = new Aliyun\OTS\OTSClient(array(
            'EndPoint' => "<your endpoint>",
            'AccessKeyID' => "<your access id>",
            'AccessKeySecret' => "<your access key>",
            'InstanceName' => "<your instance name>"
        ));                        
    ```

2.  Configure the OTSClient instance.

    To modify the default configurations of the OTSClient instance, import the corresponding parameters such as the proxy, connection timeout time, and the maximum number of connections when you create the OTSClient instance. The following table lists the parameters you can configure when you create the OTSClient instance.

    |Parameter|Description|Default Value|
    |:--------|:----------|:------------|
    |ConnectionTimeout|The maximum latency allowed to connect to Tablestore.|2.0 seconds|
    |StsToken|The STS token for temporary access.|null|
    |SocketTimeout|The maximum latency allowed for the response to each request.|2.0 seconds. We recommend that you set this parameter to a large value when large volumes of data are transmitted.|
    |RetryPolicy|The retry policy.|DefaultRetryPolicy. A value of null indicates that RetryPolicy is disabled.|
    |ErrorLogHandler|The function to process error logs, which is used to display the logs of errors returned by Tablestore.|defaultOTSErrorLogHandler. A value of null indicates that ErrorLogHandler is disabled.|
    |DebugLogHandler|The function to process debug logs, which is used to display the logs of normal requests and responses.|defaultOTSDebugLogHandler. A value of null indicates that defaultOTSDebugLogHandler is disabled.|


## HTTPS

Install the OpenSSL PHP extension.

