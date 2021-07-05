# Create instances

Instances are the basic unit to manage resources in Tablestore. Tablestore allows you to control access and charges resources for each instance. After you create an instance in the console, you can create and manage tables for the instance.

Tablestore is activated. For more information, see [Activate Tablestore](/intl.en-US/Quick Start/Activate Tablestore.md).

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  At the top of the page, select a region such as China \(Hangzhou\) or China \(Shenzhen\).

    The console displays the instance types that are available in the region.

3.  Click **Create Instance**.

4.  In the Create Instance dialog box, set **Instance Name**, **Instance Type**, and **Instance Description**.

    For more information about the naming conventions of instances and how to select an instance type, see [Instance](/intl.en-US/Function Introduction/Terms/Instance.md).

    **Note:**

    -   The instance type cannot be modified after the instance is created.
    -   A maximum of 10 instances can be created in an Alibaba Cloud account. The names of instances must be unique within a region in which the instances reside.
5.  Click **OK**.


After the instance is created, you can view the existing instances in the selected region on the **Overview** page. On the All Instances page, you can view all the created instances by region.

On the **Overview** page, you can perform the following operations on an instance:

-   Click the name of the instance or click Manage Instance in the **Actions** column corresponding to the instance. On the Instance Management page, click each tab to perform various operations.
    -   On the Instance Details tab, you can view the Instance Access URL, Basic Information, and Tables sections.
    -   On the Instance Monitoring tab, you can view monitoring data by using time ranges, metric categories, and operations.
    -   On the Network Management tab, you can modify the network type used to access the instance. You can bind a VPC to or unbind the VPC from the instance and view the list of VPCs that are bound to the instance.
    -   On the Deliver Data to OSS tab, you can create a data delivery task to deliver data from a Tablestore table to an OSS bucket and store the data in the bucket. For more information, see [Quick start](/intl.en-US/Function Introduction/Data Delivery/Quick start.md).

        **Note:** Data delivery is available in the China \(Hangzhou\), China \(Shanghai\), China \(Beijing\), and China \(Zhangjiakou\) regions.

-   Click **Release** in the **Actions** column to release an instance.

    **Note:**

    -   Before you release an instance, ensure that all tables are deleted, and VPCs are unbound from instances.
    -   To create an instance when you release an existing instance, ensure that the name of the instance you want to create is different from that of the existing instance to avoid conflicts.

