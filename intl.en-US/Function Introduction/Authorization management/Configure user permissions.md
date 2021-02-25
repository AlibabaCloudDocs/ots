# Configure user permissions

The permission management mechanism of Alibaba Cloud includes Resource Access Management \(RAM\) and Security Token Service \(STS\). RAM users that have different permissions can access Tablestore. The permission management mechanism also authorizes STS. RAM and Security Token Service \(STS\) make management more flexible and secure.

## Background information

RAM and STS enable you to grant permissions without exposing the AccessKey pair of your Alibaba Cloud account. If the AccessKey pair of the Alibaba Cloud account is leaked, other users can perform operations on all the resources of the Alibaba Cloud account and steal important information.

-   RAM is a service provided by Alibaba Cloud. RAM allows you to manage user identities and resource access permissions.

    RAM allows you to create and manage multiple identities in an Alibaba Cloud account, and grant different permissions to a single identity or a group of identities. In this way, you can authorize different identities to access different Alibaba Cloud resources. For more information, see [What is RAM?](/intl.en-US/Product Introduction/What is RAM?.md).

-   STS allows you to manage temporary access from other users to your Alibaba Cloud resources.

    You can use STS to grant temporary access tokens to RAM entities such as RAM users and RAM roles. You can customize the validity period and access permissions of these STS tokens. For more information, see [What is STS?](/intl.en-US/API Reference/API Reference (STS)/What is STS?.md).


RAM is an access control service that provides long-term permission management mechanism. The owner of an Alibaba Cloud account can create RAM users and grant different permissions to the RAM users. This way, if an AccessKey pair of a RAM user is disclosed, the information that is leaked is limited. RAM users remain valid for a long period of time. The AccessKey pairs of RAM users must be kept confidential.

In contrast to the long-term permission management mechanism provided by RAM, STS provides temporary access authorization by using a temporary AccessKey pair and token to allow temporary access to Tablestore. Permissions obtained from STS are strictly restricted and have time limits. Therefore, even if information is disclosed, your system is not severely affected.

## Grant permissions to a RAM user

1.  Create a RAM user. For more information, see [Create a RAM user](/intl.en-US/RAM User Management/Create a RAM user.md).
2.  Grant permissions to the RAM user. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

    -   To manage Tablestore such as creating an instance, grant the AliyunOTSFullAccess permission to the RAM user.
    -   If the RAM user requires read-only access to Tablestore such as reading data from a table, grant the AliyunOTSReadOnlyAccess permission to the RAM user.
    -   If the RAM user requires write-only access to Tablestore such as creating a table, grant the AliyunOTSWriteOnlyAccess permission to the RAM user.
    **Note:** For more information about how to implement finer-grained permission control and configure policies, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md). For more information, see [Custom permissions](/intl.en-US/Function Introduction/Authorization management/Custom permissions.md).

3.  Enable a multi-factor authentication \(MFA\) device for a RAM user. For more information, see [Enable an MFA device for a RAM user](/intl.en-US/Security Settings/Multi-factor authentication/Enable an MFA device for a RAM user.md).

## Grant permissions to a temporary user

1.  Create a temporary role and grant permissions.
    1.  Create a RAM role for a trusted Alibaba Cloud account. For more information, see [Create a RAM role for a trusted Alibaba Cloud account](/intl.en-US/RAM Role Management/Create a RAM role/Create a RAM role for a trusted Alibaba Cloud account.md).

        Create two roles named RamTestAppReadOnly and RamTestAppWrite. RamTestAppReadOnly is used to read data, and RamTestAppWrite is used to upload files.

    2.  Create a custom policy. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

        **Note:** To implement finer-grained permission control, you can customize the permissions of a policy. For more information, see [Custom permissions](/intl.en-US/Function Introduction/Authorization management/Custom permissions.md).

        Create two policies named ram-test-app-readonly and ram-test-app-write.

        -   Ram-test-app-readonly

            ```
            {
            "Statement": [
                {
                  "Effect": "Allow",
                  "Action": [
                    "ots:BatchGet*",
                    "ots:Describe*",
                    "ots:Get*",
                    "ots:List*"
                  ],
                  "Resource": [
                    "acs:ots:*:*:instance/ram-test-app",
                    "acs:ots:*:*:instance/ram-test-app/table/*"
                  ]
                }
            ],
            "Version": "1"
            }                        
            ```

        -   ram-test-app-write

            ```
             {
             "Statement": [
                    {
                      "Effect": "Allow",
                      "Action": [
                        "ots:Create*",
                        "ots:Insert*",
                        "ots:Put*",
                        "ots:Update*",
                        "ots:Delete*",
                        "ots:BatchWrite*"
                      ],
                      "Resource": [
                        "acs:ots:*:*:instance/ram-test-app",
                        "acs:ots:*:*:instance/ram-test-app/table/*"
                      ]
                    }
             ],
             "Version": "1"
             }                        
            ```

    3.  Grant permissions to a temporary role. For more information, see [Grant permissions to a RAM role](/intl.en-US/RAM Role Management/Grant permissions to a RAM role.md).

        Assign the ram-test-app-readonly policy to RamTestAppReadOnly. These are read-only permissions on Tablestore. Assign the ram-test-app-write policy to RamTestAppWrite. These are write-only permissions on Tablestore.

        After you complete the authorization, record the **ARN** of the role. ARN indicates the ID of the role that the RAM user assumes. The following figure shows the ARN.

        ![fig_arn](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/8934808061/p178120.png)

2.  Grant temporary access permissions.
    1.  Create a custom policy. For more information, see [Create a custom policy](/intl.en-US/Policy Management/Custom policies/Create a custom policy.md).

        **Note:** To implement finer-grained permission control, you can customize the permissions of a policy. For more information, see [Custom permissions](/intl.en-US/Function Introduction/Authorization management/Custom permissions.md).

        Create two policies named AliyunSTSAssumeRolePolicy 2016011401 and AliyunSTSAssumeRolePolicy 2016011402. Resource indicates the ARN of the role.

        -   AliyunSTSAssumeRolePolicy2016011401

            ```
            {
            "Version": "1",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": "sts:AssumeRole",
                    "Resource": "acs:ram:198***237:role/ramtestappreadonly"
                }
            ]
            }                                
            ```

        -   AliyunSTSAssumeRolePolicy2016011402

            ```
            {
            "Version": "1",
            "Statement": [
                {
                    "Effect": "Allow",
                    "Action": "sts:AssumeRole",
                    "Resource": "acs:ram:198***237:role/ramtestappwrite"
                }
            ]
            }                               
            ```

    2.  Grant permissions to the temporary role that the RAM user assumes. For more information, see [Grant permissions to a RAM user](/intl.en-US/RAM User Management/Grant permissions to a RAM user.md).

        Grant the custom AliyunSTSAssumeRolePolicy2016011401 and AliyunSTSAssumeRolePolicy2016011402 policies to the RAM user named ram\_test\_app.

3.  Obtain temporary access credentials from STS. For more information, see [AssumeRole](/intl.en-US/API Reference/API Reference (STS)/Operation interfaces/AssumeRole.md).
4.  Use temporary permissions to read and write data.

    You can use the temporary permission to call the SDKs of different programming languages to access Tablestore. You can use the following method to create an OTSClient object and add parameters obtained from STS such as AccessKeyId, AccessKeySecret, and SecurityToken:

    ```
    OTSClient client = new OTSClient(otsEndpoint, stsAccessKeyId, stsAccessKeySecret, instanceName, stsToken);           
    ```


