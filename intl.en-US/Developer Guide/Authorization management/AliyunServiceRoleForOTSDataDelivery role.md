AliyunServiceRoleForOTSDataDelivery role 
=============================================================

When you use data delivery of Tablestore, you must have permissions to access OSS resources. To obtain permissions to access OSS resources, you can create a Tablestore service linked role AliyunServiceRoleForOTSDataDelivery in the Tablestore console.
**Note**

For more information about service linked roles, see [Service linked roles](/intl.en-US/RAM Role Management/Service linked roles.md).

Create a service lined role 
------------------------------------------------

When you use data delivery of Tablestore, you can create a Tablestore service linked role AliyunServiceRoleForOTSDataDelivery in the Tablestore console.

The policy corresponding to AliyunServiceRoleForOTSDataDelivery is AliyunServiceRolePolicyForOTSDataDelivery. The following operations on OSS are supported: PutObject, AbortMultipartUpload, PutObjectTagging, GetObject, and DeleteObjectTagging.

Delete a service lined role 
------------------------------------------------

Before you delete a Tablestore service linked role AliyunServiceRoleForOTSDataDelivery, make sure that data delivery is unnecessary for all instances in the current account.
**Notice**

After you delete a Tablestore service linked role, data in the current account cannot be delivered to OSS.

To delete a service linked role, perform the following operations:

1. Log on to the [RAM console](https://ram.console.aliyun.com/).

   

2. In the left-side navigation pane, click **RAM Roles** .

   

3. On the **RAM Roles** page, enter AliyunServiceRoleForOTSDataDelivery in the search box. The AliyunServiceRoleForOTSDataDelivery role is displayed.

   

4. Click **Delete** in the **Actions** column corresponding to the RAM role.

   

5. In the **Delete RAM Role** message, click **OK** .

   * If data delivery is configured for instances in the current account, this role cannot be deleted. To delete the role, delete the delivery tasks in the instances.

     
   
   * This role can be deleted if no instances in the current account are using data delivery.

     
   

   




FAQ 
------------------------

Why cannot the system create a Tablestore service linked role (AliyunServiceRoleForOTSDataDelivery) for a RAM user?

The system creates a Tablestore service linked role only for users that have the specified permission. To create a Tablestore service linked role for a RAM user, you must add the following policy for the RAM user.

Specify the *ID of the Alibaba Cloud account* for the policy.

    {
        "Statement": [
            {
                "Action": [
                    "ram:CreateServiceLinkedRole"
                ],
                "Resource": "acs:ram:*:the ID of the Alibaba Cloud account:role/*",
                "Effect": "Allow",
                "Condition": {
                    "StringEquals": {
                        "ram:ServiceName": [
                            "arms.aliyuncs.com"
                        ]
                    }
                }
            }
        ],
        "Version": "1"
    }



