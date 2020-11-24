# Initialization

OTSClient is the client for Tablestore. OTSClient provides a series of methods for you to manage tables and perform read and write operations on one or more rows.

## Obtain an endpoint

An endpoint is the domain name that is used to access a Tablestore instance in a region. To query an endpoint, perform the following operations:

1.  Log on to the Tablestore console.

2.  Click the instance name to go to the Instance Management page.

    On the Instance Details tab, you can view the endpoints of the instance in Instance Access URL.


**Note:** For more information about endpoints, see [Endpoint](/intl.en-US/Function Introduction/Terms/Endpoint.md).

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

## Initializa an OTSClient instance

After you obtain the AccessKey ID and AccessKey secret, you can perform the following operations to initialize a Tablestore client instance:

-   Operations

    ```
        /// <summary>
        /// The function used to construct OTSClient.
        /// </summary>
        /// <param name="endPoint"> The endpoint of the Tablestore instance, which must start with https://. Example: https://instance.cn-hangzhou.ots.aliyun.com:80 </param>
        /// <param name="accessKeyID"> The AccessKey ID that is used to access the Tablestore instance. You can apply for an AccessKey ID on the Alibaba Cloud official website. </param>
        /// <param name="accessKeyID"> The AccessKey secret that is used to access the Tablestore instance. You can apply for an AccessKey secret on the Alibaba Cloud official website. </param>
        /// <param name="instanceName"> The name of the Tablestore instance. You can customize the instance name in the Tablestore console. </param>
    public OTSClient(string endPoint, string accessKeyID, string accessKeySecret, string instanceName);
    
        /// <summary>
        /// Create an OTSClient instance by configuring the instance of the OTSClientConfig class on the client.
        /// </summary>
        /// <param name="config"> The instance for client configurations.</param>
        public OTSClient(OTSClientConfig config);
                        
    ```

-   Examples

    **Note:**

    -   You can set the ConnectionLimit parameter in the OTSClientConfig instance. If you do not specify the value of the ConnectionLimit parameter, the default value 300 is used.
    -   You can set OTSDebugLogHandler and OTSErrorLogHandler in OTSClientConfig to specify whether to enable logging.
    -   You can specify RetryPolicy in OTSClientConfig to control and customize the retry logic. You can also use the default retry policy.
    ```
        // Create an OTSClientConfig object.
        var config = new OTSClientConfig(Endpoint, AccessKeyId, AccessKeySecret, InstanceName);
    
        // Disable logging. By default, logging is enabled.
        config.OTSDebugLogHandler = null;
        config.OTSErrorLogHandler = null;
    
        // Create an OTSClient object by using OTSClientConfig.
        var otsClient = new OTSClient(config);
    
        // Use OTSClient to insert or query data.
                        
    ```


## Multithreading

-   Multithreading is supported.
-   We recommend that you use the same OTSClient object to perform multithreading tasks.

