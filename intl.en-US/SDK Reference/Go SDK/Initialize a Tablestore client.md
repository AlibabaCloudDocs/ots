# Initialize a Tablestore client

TableStoreClient is the client for Tablestore. TableStoreClient provides a series of methods for you to manage tables and perform read and write operations on one or more rows.

## Obtain an endpoint

An endpoint is the domain name that is used to access a Tablestore instance in a region. To query an endpoint, perform the following operations:

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

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

## Initialize TableStoreClient

After you obtain the AccessKey ID and AccessKey secret, you can perform the following operations to initialize a Tablestore client instance:

-   Operations

    ```
    // Initialize the TableStoreClient instance.
    // endPoint indicates the endpoint used to access the Tablestore instance, which must start with https://. Example: https://instance.cn-hangzhou.ots.aliyun.com:80
    // accessKeyId indicates the AccessKey ID used to access the Tablestore instance. You can visit the official website or contact an administrator to obtain an AccessKey ID.
    // accessKeySecret indicates the AccessKey secret used to access the Tablestore instance. You can visit the official website or contact an administrator to obtain an AccessKey secret.
    // instanceName indicates the name of the instance you want to access. You can create an instance in the Tablestore console or contact an administrator to obtain the name of an existing instance.
    func NewClient(endPoint, instanceName, accessKeyId, accessKeySecret string, options ...ClientOption) *TableStoreClient
                        
    ```

-   Examples

    ```
    client = tablestore.NewClient("your_instance_endpoint",  "your_instance_name", "your_user_id", "your_user_key")          
    ```


