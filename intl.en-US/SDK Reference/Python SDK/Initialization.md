# Initialization

OTSClient is the client for Tablestore. It provides a series of methods for you to manage tables and perform read and write operations on one or more rows.

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

-   Operations

    ```
            """
            Initialize the OTSClient instance.
            end_point indicates the endpoint used to access the Tablestore instance, which must start with https://. Example: https://instance.cn-hangzhou.ots.aliyun.com:80
            access_key_id indicates the AccessKey ID used to access the Tablestore instance. You can visit the Alibaba Cloud official website or contact an administrator to obtain an AccessKey ID.
            access_key_secret indicates the AccessKey secret used to access the Tablestore instance. You can visit the Alibaba Cloud official website or contact an administrator to obtain an AccessKey secret.
            instance_name indicates the name of the instance you want to access. You can create an instance in the Tablestore console or contact an administrator to obtain the name of an existing instance.
            sts_token indicates the STS token used to access the Tablestore instance. You can use Alibaba Cloud STS to obtain an STS token. The STS token has a validity period. You must obtain a new token after the existing token expires.
            encoding indicates the method used to encode the request parameter string. The default value is utf8.
            socket_timeout indicates the timeout period in seconds for each socket connection in the connection pool. It can be an integer or a float number. The default value is 50.
            max_connection indicates the maximum number of connections in a connection pool. The default value is 50.
            logger_name is used to display the DEBUG log in the request or display the ERROR log when an error occurs.
            retry_policy defines the retry policy. The default retry policy is DefaultRetryPolicy. You can define a retry policy based on a RetryPolicy class. For more information, see the code in DefaultRetryPolicy.
            """
            class OTSClient(object):
                def __init__(self, endpoint, access_key_id, access_key_secret, instance_name, **kwargs):
                        
    ```

-   Examples

    ```
        #########    Configure the name of the log file and retry policy.     #########
        # The name of the log file is table_store.log. The retry policy is WriteRetryPolicy, which defines the retry attempts to write the table when a write operation fails.
        ots_client = OTSClient('endpoint', 'access_key_id', 'access_key_secret', 'instance_name', logger_name = 'table_store.log',  retry_policy = WriteRetryPolicy())
    
    
        #########    Use STS     #########
        ots_client = OTSClient('endpoint', 'STS.K8h*******GB77', 'CkuDj******Wn6', 'instance_name', sts_token = 'CAISjgJ1q6Ft5B2y********OFcsLLuw==')
                        
    ```


## HTTPS

-   HTTPS is supported in Python 2.0.8 and later.
-   OpenSSL 0.9.8j or later is required. We recommend that you use OpenSSL 1.0.2d.
-   The Python 2.0.8 release package contains a certifi package that can be installed and used. To update the root certificate, download the latest root certificate from [Root Certificate](https://pypi.python.org/pypi/certifi).

