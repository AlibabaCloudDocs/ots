# Initialization

OTSClient is the client for Tablestore. OTSClient provides a series of methods for you to manage tables and perform read and write operations on one or more rows. To use Tablestore SDK for Java to initiate a request, you must initialize an OTSClient instance and modify the default configuration items of ClientConfiguration.

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

Create a client

When you use Tablestore SDKs, you must create a client and then call the operations in the client to access Tablestore. The operations in the client provide the same functions as the RESTful API provided by Tablestore.

Tablestore SDKs offer two client types: SyncClient and AsyncClient. SyncClient is designed for synchronous operations and AsyncClient is for asynchronous operations. After a synchronous operation is called, the service request succeeds. You can call synchronous operations to learn more about the various functions of Tablestore. Compared with synchronous operations, asynchronous operations provide greater flexibility. If you require high performance, you can choose between the use of asynchronous operations and multithreading.

**Note:** Both SyncClient and AsyncClient are thread-safe and can internally manage threads and connection resources. You do not need to create a client for each thread or request. Instead, create a global client.

Examples

-   The following code provides an example on how to use the default configurations to create a SyncClient:

    ```
            final String endPoint = ""; 
            final String accessKeyId = ""; 
            final String accessKeySecret = ""; 
            final String instanceName = "";
            SyncClient client = new SyncClient(endPoint, accessKeyId, accessKeySecret, instanceName);
                            
    ```

-   The following code provides an example on how to use the custom configurations to create a SyncClient:

    ```
            // ClientConfiguration provides multiple configuration items. Only the most commonly used items are listed.
            ClientConfiguration clientConfiguration = new ClientConfiguration();
            // Set the timeout period for establishing a connection.
            clientConfiguration.setConnectionTimeoutInMillisecond(5000);
            // Set the timeout period for the socket connection.
            clientConfiguration.setSocketTimeoutInMillisecond(5000);
            // Configure a retry policy. If you do not configure policies, the default retry policy is used.
            clientConfiguration.setRetryStrategy(new AlwaysRetryStrategy());
            SyncClient client = new SyncClient(endPoint, accessId, accessKey, instanceName, clientConfiguration);
    ```


## HTTPS

Upgrade to Java 7 for HTTPS access.

## Multithreading

-   Multithreading is supported.
-   We recommend that you use the same OTSClient object to perform multithreading tasks.

