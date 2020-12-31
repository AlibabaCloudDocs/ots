# Custom permissions

This topic describes the definitions and application scenarios of Action, Resource, and Condition.

## Action

Action defines the specific API operation or operations to allow or deny. When you create a Tablestore authorization policy, add the `ots:` prefix to each API operation and separate different API operations with commas \(,\). The asterisk \(\*\) wildcard is used in Action to specify the prefix matching and suffix matching.

Action has the following definitions:

-   Single API operation

    ```
    "Action": "ots:GetRow"
    					
    ```

-   Multiple API operations

    ```
    "Action": [
    "ots:PutRow",
    "ots:GetRow"
    ]
    					
    ```

-   All read-only API operations

    ```
    {
      "Version": "1",
      "Statement": [
        {
          "Action": [
            "ots:BatchGet*",
            "ots:Describe*",
            "ots:Get*",
            "ots:List*",
            "ots:Consume*",
            "ots:Search",
            "ots:ComputeSplitPointsBySize"
          ],
          "Resource": "*",
          "Effect": "Allow"
        }
      ]
    }                
    ```

-   All read and write API operations

    ```
    "Action": "ots:*"               
    ```


## Resource

Resource in Tablestore consists of multiple fields including the service, region, user\_id, instance\_name, and table\_name. Each field supports asterisk \(\*\) wildcards for prefix and suffix matching. Resource is configured in the following format:

```
acs:ots:[region]:[user_id]:instance/[instance_name]/table/[table_name]
			
```

The fields enclosed in brackets are variables. The value of the service field must be ots. The value of the region field specifies the region ID, such as cn-hangzhou. The value of the user\_id field specifies the ID of the Alibaba Cloud account.

**Note:**

-   Tablestore instance names are not case-sensitive. However, the instance\_name field in Resource must be in lower case.
-   Resource is defined for Tunnel Service by instance rather than table and includes fields such as service, region, user\_id, and instance\_name in the definition. Tunnel is configured in the following format:

    ```
    acs:ots:[region]:[user_id]:instance/[instance_name]
    							
    ```


Resource has the following definitions:

-   All resources of users in all regions

    ```
    "Resource": "acs:ots:*:*:*"
    					
    ```

-   All instances and their tables of User 123456 in the China \(Hangzhou\) region

    ```
    "Resource": "acs:ots:cn-hangzhou:123456:instance/*"
    					
    ```

-   Instance abc and its tables of User 123456 in the China \(Hangzhou\) region

    ```
    "Resource": [
    "acs:ots:cn-hangzhou:123456:instance/abc",
    "acs:ots:cn-hangzhou:123456:instance/abc/table/*"
    ]
    					
    ```

-   All instances whose names contain the prefix abc and their tables

    ```
    "Resource": "acs:ots:*:*:instance/abc*"
    					
    ```

-   All tables whose names contain the prefix xyz in the instances whose names contain the prefix abc. Instance resources are not included. acs:ots:\*:\*:instance/abc\* does not match this definition.

    ```
    "Resource": "acs:ots:*:*:instance/abc*/table/xyz*"
    					
    ```

-   All instances whose names contain the suffix abc and their tables whose names contain the suffix xyz

    ```
    "Resource": [
    "acs:ots:*:*:instance/*abc",
    "acs:ots:*:*:instance/*abc/table/*xyz"
    ]
    					
    ```


## Tablestore API operations

Tablestore provides the following types of API operations:

-   Instance management operations
-   Operations for reading and writing tables and data
-   Operations for managing, reading, and writing real-time tunnels

The following tables provide details about these API operations:

-   Resources for management API operations

    Management API operations are instance-based operations and can only be called by using the console. Specifying Action and Resource for Management API operations determines subsequent use of the console. The `acs: ots: [region]: [user_id]:` prefix is omitted in the following accessed resources. Only the instance and table are described.

    |API operation/Action|Resource|
    |:-------------------|:-------|
    |ListInstance|instance/\*|
    |InsertInstance|instance/\[instance\_name\]|
    |GetInstance|instance/\[instance\_name\]|
    |DeleteInstance|instance/\[instance\_name\]|

-   Resources for data API operations

    Data API operations are table- and row-based operations, which can be called by using the console or Tablestore SDK. Specifying Action and Resource for data API operations determines subsequent use of the console. The `acs:ots:[region]:[user_id]:` prefix is omitted in the following accessed resources. Only the instance and table are described.

    |API operation/Action|Resource|
    |:-------------------|:-------|
    |ListTable|instance/\[instance\_name\]/table/\*|
    |CreateTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |UpdateTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |DescribeTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |DeleteTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |GetRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |PutRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |UpdateRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |DeleteRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |GetRange|instance/\[instance\_name\]/table/\[table\_name\]|
    |BatchGetRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |BatchWriteRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |ComputeSplitPointsBySize|instance/\[instance\_name\]/table/\[table\_name\]|
    |StartLocalTransaction|instance/\[instance\_name\]/table/\[table\_name\]|
    |CommitTransaction|instance/\[instance\_name\]/table/\[table\_name\]|
    |AbortTransaction|instance/\[instance\_name\]/table/\[table\_name\]|
    |CreateIndex|instance/\[instance\_name\]/table/\[table\_name\]|
    |DropIndex|instance/\[instance\_name\]/table/\[table\_name\]|
    |CreateSearchIndex|instance/\[instance\_name\]/table/\[table\_name\]|
    |DeleteSearchIndex|instance/\[instance\_name\]/table/\[table\_name\]|
    |ListSearchIndex|instance/\[instance\_name\]/table/\[table\_name\]|
    |DescribeSearchIndex|instance/\[instance\_name\]/table/\[table\_name\]|
    |Search|instance/\[instance\_name\]/table/\[table\_name\]|
    |CreateTunnel|instance/\[instance\_name\]/table/\[table\_name\]|
    |DeleteTunnel|instance/\[instance\_name\]/table/\[table\_name\]|
    |ListTunnel|instance/\[instance\_name\]/table/\[table\_name\]|
    |DescribeTunnel|instance/\[instance\_name\]/table/\[table\_name\]|
    |ConsumeTunnel|instance/\[instance\_name\]/table/\[table\_name\]|

-   Resources for Tunnel Service API operations

    API operations for Tunnel Service are instance-based operations and can be called by using the console or by the SDK. Specifying Action and Resource for Tunnel Service API operations determines subsequent use of the console. The `acs:ots:[region]:[user_id]:` prefix is omitted in the following accessed resources. Only the instance and table are described.

    |API operation/Action|Resource|
    |:-------------------|:-------|
    |ListTable|instance/\[instance\_name\]|
    |CreateTable|instance/\[instance\_name\]|
    |UpdateTable|instance/\[instance\_name\]|
    |DescribeTable|instance/\[instance\_name\]|
    |DeleteTable|instance/\[instance\_name\]|
    |GetRow|instance/\[instance\_name\]|
    |PutRow|instance/\[instance\_name\]|
    |UpdateRow|instance/\[instance\_name\]|
    |DeleteRow|instance/\[instance\_name\]|
    |GetRange|instance/\[instance\_name\]|
    |BatchGetRow|instance/\[instance\_name\]|
    |BatchWriteRow|instance/\[instance\_name\]|
    |ComputeSplitPointsBySize|instance/\[instance\_name\]|
    |StartLocalTransaction|instance/\[instance\_name\]|
    |CommitTransaction|instance/\[instance\_name\]|
    |AbortTransaction|instance/\[instance\_name\]|
    |CreateIndex|instance/\[instance\_name\]|
    |DropIndex|instance/\[instance\_name\]|
    |CreateSearchIndex|instance/\[instance\_name\]|
    |DeleteSearchIndex|instance/\[instance\_name\]|
    |ListSearchIndex|instance/\[instance\_name\]|
    |DescribeSearchIndex|instance/\[instance\_name\]|
    |Search|instance/\[instance\_name\]|
    |CreateTunnel|instance/\[instance\_name\]|
    |DeleteTunnel|instance/\[instance\_name\]|
    |ListTunnel|instance/\[instance\_name\]|
    |DescribeTunnel|instance/\[instance\_name\]|
    |ConsumeTunnel|instance/\[instance\_name\]|

-   Instructions
    -   Action and Resource in a policy are verified by string matching. The asterisk \(\*\) wildcard is used to specify the prefix matching and suffix matching. If Resource is defined as acs:ots:\*:\*:instance/\*/, acs:ots:\*:\*:instance/abc cannot be matched. If Resource is defined as acs:ots:\*:\*:instance/abc, acs:ots:\*:\*:instance/abc/table/xyz cannot be matched.

    -   To use a RAM user to manage instance resources by using the Tablestore console, the RAM user must be granted read permissions on acs:ots:\[region\]:\[user\_id\]:instance/\* because the console needs to obtain the instance list.

    -   For batch API operations, such as BatchGetRow and BatchWriteRow, the backend service authenticates each table to be accessed. Operations can be performed only when all tables are authenticated. Otherwise, an error message is returned.


## Condition

Policies can support a variety of authentication conditions, including IP address-based access control, HTTPS-based access control, Multi-Factor Authentication \(MFA\)-based access control, and time-based access control. These conditions are supported by all Tablestore API operations.

-   IP address-based access control

    RAM allows you to specify IP addresses or CIDR blocks that are used to access Tablestore resources. IP address-based access control applies to the following scenarios:

    -   Specify multiple IP addresses. The following code indicates that only access requests from the IP addresses 10.101.168.111 and 10.101.169.111 are allowed:

        ```
        {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.168.111",
                            "10.101.169.111"
                        ]
                    }
                }
            }
        ],
        "Version": "1"
        }
        							
        ```

    -   Specify one IP address or CIDR block. The following code indicates that only access requests from the IP address 10.101.168.111 or the CIDR block 10.101.169.111/24 are allowed:

        ```
        {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.168.111",
                            "10.101.169.111/24"
                        ]
                    }
                }
            }
        ],
        "Version": "1"
        }
        							
        ```

-   HTTPS-based access control

    RAM allows you to specify whether resources must be accessed by requests over HTTPS.

    The following example indicates that Tablestore resources must be accessed by requests over HTTPS.

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "Bool": {
                        "acs:SecureTransport": "true"
                    }
                }
            }
        ],
        "Version": "1"
    }
    					
    ```

-   MFA-based access control

    RAM allows you to specify whether resources must be accessed by requests that have passed MFA.

    The following example indicates that Tablestore resources must be accessed by requests that have passed MFA.

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "Bool": {
                        "acs:MFAPresent ": "true"
                    }
                }
            }
        ],
        "Version": "1"
    }
    					
    ```

-   Time-based access control

    RAM allows you to specify the access time of requests. Access requests earlier than the specified time are allowed or denied. The following example shows a typical application scenario.

    Example: RAM users are allowed to access resources only before 00:00:00 January 1, 2016 \(UTC+8\).

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "DateLessThan": {
                        "acs:CurrentTime": "2016-01-01T00:00:00+08:00"
                    }
                }
            }
        ],
        "Version": "1"
    }
    					
    ```


## Scenarios

This section describes specific policies in typical scenarios and offers authorization methods based on the definitions of Action, Resource, and Condition.

-   Multiple authorization conditions

    In this scenario, RAM users using the 10.101.168.111/24 CIDR block are allowed to read from and write to all instances named online-01 and online-02 \(including all tables of these instances\). Access is only allowed before 0:00:00 January 1, 2016, and all access requests must be made over HTTPS.

    Perform the following steps to configure multiple authorization conditions:

    1.  Log on to the [RAM console](https://ram.console.aliyun.com/) with an Alibaba Cloud account. \(RAM is activated.\)
    2.  In the left-side navigation pane, choose **Permissions** \> **Policies** to go to the Policies page.
    3.  Click **Create Policy** to go to the Create Custom Policy page.
    4.  Enter **Policy Name** and select **Script** as the Configuration Mode. Enter the following content in the **Policy Document** field:

        ```
        {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": [
                    "acs:ots:*:*:instance/online-01",
                    "acs:ots:*:*:instance/online-01/table/*",
                    "acs:ots:*:*:instance/online-02",
                    "acs:ots:*:*:instance/online-02/table/*"
                ],
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.168.111/24"
                        ]
                    },
                    "DateLessThan": {
                        "acs:CurrentTime": "2016-01-01T00:00:00+08:00"
                    },
                    "Bool": {
                        "acs:SecureTransport": "true"
                    }
                }
            }
        ],
        "Version": "1"
        }
        							
        ```

    5.  Click **OK**.
    6.  In the left-side navigation pane, choose **Identities** \> **Users**. On the Users page, click **Add Permissions** in the Actions column corresponding to a RAM user.
    7.  In the Add Permissions dialog box, search for the newly created policy, and click the policy to add the permissions to the Selected column. Click **OK**. The selected permissions are granted to the RAM user.
-   Reject requests

    In this scenario, RAM users using the IP address 10.101.169.111 are not allowed to write to any tables that belong to instances prefixed with online or product and located in the China \(Beijing\) region. This policy does not define actions and permissions on instances.

    To reject requests, perform the steps described in the preceding "Multiple authorization conditions" section to create a new policy and grant policy permissions to the designated RAM user. Copy the following content to **Policy Document** during policy creation:

    ```
    {
        "Statement": [
            {
                "Effect": "Deny",
                "Action": [
                    "ots:Create*",
                    "ots:Insert*",
                    "ots:Put*",
                    "ots:Update*",
                    "ots:Delete*",
                    "ots:BatchWrite*"
                ],
                "Resource": [
                    "acs:ots:cn-beijing:*:instance/online*/table/*",
                    "acs:ots:cn-beijing:*:instance/product*/table/*"
                ],
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.169.111"
                        ]
                    }
                }
            }
        ],
        "Version": "1"
    }
    
    					
    ```


