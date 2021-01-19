# \(Optional\) Bind VPCs

After you bind a virtual private cloud \(VPC\) to a Tablestore instance, you can access the Tablestore instance from ECS instances in the VPC if the Tablestore instance and the ECS instances are located within the same region.

## Prerequisites

A VPC is created. For more information, see [Create an IPv4 VPC network](/intl.en-US/Quick Start/Create an IPv4 VPC network.md)..

## Procedure

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the required instance or click **Manage Instance** in the Actions column corresponding to the instance.

3.  Click the **Network Management** tab.

4.  On the Network Management tab, click **Bind VPC**.

    ![fig_tablestore_009](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/en-US/7814916061/p96310.png)

5.  Select a VPC and vSwitch. Set **Instance VPC Name**.

6.  Click **OK**.

    After the VPC is bound to the instance, you can view the information of the VPC in the **VPCs** section on the Network Management tab. You can use the VPC address to access the Tablestore instance from ECS instances in the VPC.

    You can click **Unbind** in the Actions column corresponding to the Tablestore instance to unbind the VPC from the instance. After the VPC is unbound, ECS instances in the VPC can no longer use VPC addresses to access the Tablestore instance. To access the Tablestore instance from the ECS instances, you must bind the VPC to the Tablestore instance again.

    **Note:** To log on to the VPC console to manage the VPC as a RAM user, make sure that an Alibaba Cloud account is used to authorize the RAM user by attaching the AliyunVPCReadOnlyAccess policy to the RAM user in the [RAM console](https://ram.console.aliyun.com/?spm=a2c4g.11186623.2.7.67ed198fFi9zEl#/user/list). Otherwise, you are not authorized to obtain related information about the VPC.


