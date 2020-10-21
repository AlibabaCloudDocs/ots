# 将MaxCompute数据同步到表格存储

通过DataWorks控制台将MaxCompute中的全量数据同步到表格存储。

表格存储能够支持千万TPS以及毫秒级延迟的服务能力，拥有强大的读写能力，同时表格存储还提供多元索引等强大的索引功能，满足各种搜索场景。您可以将MaxCompute计算分析后的数据同步到表格存储中，提升应用的读写和搜索效率。

![tablestore](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/5589997951/p59920.png)

## 步骤一：新增数据源

将表格存储数据库添加为数据源，具体操作步骤如下：

1.  以项目管理员身份登录[DataWorks控制台](https://workbench.data.aliyun.com/console)。

    **说明：** 仅项目管理员角色可以新增数据源，其他角色的成员仅可查看数据源。

2.  选择区域，在左侧导航栏，单击工作空间列表。

3.  在工作空间列表页面，单击工作空间操作区域的**进入数据集成**。

4.  在数据集成控制台，单击**数据源管理**。

5.  新增数据源。

    1.  在数据源管理页面，单击**新增数据源**。

    2.  在新增数据源对话框的**NoSQL**区域，选择数据源类型为**OTS**。

    3.  在新增OTS数据源对话框，配置参数。

        ![fig_otssource](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7548388951/p127135.png)

        |参数|说明|
        |--|--|
        |数据源名称|数据源的名称，例如gps\_data。|
        |数据源描述|数据源的描述信息。|
        |Endpoint|填写目标Tablestore实例的[服务地址](/cn.zh-CN/功能介绍/基础概念/服务地址.md)。         -   如果Tablestore实例和MaxCompute在同一个region，填写经典网地址。
        -   如果Tablestore实例和MaxCompute不在同一个region，填写公网地址。
        -   不能填写VPC地址。 |
        |Table Store实例名称|Tablestore实例的名称。|
        |AccessKey ID|登录账户的AccessKeyID和AccessKeySecret，获取方式请参见[为RAM用户创建访问密钥](/cn.zh-CN/安全设置/访问密钥/为RAM用户创建访问密钥.md)。|
        |AccessKey Secret|

    4.  单击**测试连通性**，测试数据源的连通状态。

6.  单击**完成**。

    在**数据源管理**页面，会显示该数据源信息。


## 步骤二：新建同步任务

新建并配置MaxCompute到表格存储的同步任务，具体操作步骤如下：

1.  以项目管理员身份登录[DataWorks控制台](https://workbench.data.aliyun.com/console)。

    **说明：** 仅项目管理员角色可以新增数据源，其他角色的成员仅可查看数据源。

2.  选择区域，在左侧导航栏，单击工作空间列表。

3.  在工作空间列表页面，单击工作空间操作中的**进入数据开发**。

4.  在DataStudio控制台的数据开发页面，单击**业务流程**节点下的目标业务流程。

    如果需要新建业务流程，请参见[创建业务流程]()。

5.  新建同步任务节点。

    每个同步任务都需创建一个相应的节点。

    1.  在**数据集成**节点上右键选择**新建** \> **离线同步**。

    2.  在新建节点对话框，输入**节点名称**。

        ![将MaxCompute数据同步到表格存储](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6589997951/p52836.png)

    3.  单击**提交**。

6.  配置数据源。

    1.  在**数据集成**节点下，双击同步任务节点。

    2.  在同步任务节点的编辑页面的选择数据源区域，配置数据来源和数据去向。

        -   配置数据来源。

            选择**数据来源**的**数据源**为**ODPS**，并选择对应的**表**。

        -   配置数据去向。

            选择**数据去向**的**数据源**为**OTS**，并单击![script](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7548388951/p127620.png)图标或者**点击转换为脚本**，进行脚本配置。

            **说明：** 表格存储仅支持脚本模式，配置脚本的详情请参见[配置Tablestore（OTS）Writer]()。

        ![将MaxCompute数据同步到表格存储](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6589997951/p52841.png)

    3.  单击![save](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/7548388951/p127623.png)图标，保存数据源配置。

7.  运行同步任务。

    1.  单击![start](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8548388951/p127635.png)图标。

    2.  在参数对话框，选择调度的资源组。

    3.  单击**确定**，开始运行任务。

        运行结束后，在运行日志页签中可以查看任务是否成功和导出的数据行数。


## 步骤三：定时执行同步任务

1.  以项目管理员身份登录[DataWorks控制台](https://workbench.data.aliyun.com/console)。

    **说明：** 仅项目管理员角色可以新增数据源，其他角色的成员仅可查看数据源。

2.  选择区域，在左侧导航栏，单击工作空间列表。

3.  在工作空间列表页面，单击工作空间操作中的**进入数据开发**。

4.  在DataStudio控制台的数据开发页面，单击**业务流程**节点下的目标业务流程。

5.  配置调度参数。

    通过**调度配置**，可以配置同步任务的执行时间、重跑属性、调度依赖等。

    1.  在**数据集成**节点下，双击同步任务节点。

    2.  在同步任务节点的编辑页面的右侧单击**调度配置**，进行调度参数配置，详情请参见[配置调度和依赖属性]()。

6.  提交同步任务。

    1.  在同步任务节点的编辑页面，单击![submit](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/8548388951/p127669.png)图标。

    2.  在提交新版本对话框，输入备注信息。

    3.  单击**确认**。

        将同步任务提交到调度系统后，调度系统会根据配置的调度参数，自动定时执行同步任务。


## 步骤四：查看同步任务

1.  以项目管理员身份登录[DataWorks控制台](https://workbench.data.aliyun.com/console)。

    **说明：** 仅项目管理员角色可以新增数据源，其他角色的成员仅可查看数据源。

2.  选择区域，在左侧导航栏，单击工作空间列表。

3.  在工作空间列表页面，单击工作空间操作中的**进入运维中心**。

4.  在运维中心控制台，选择**周期任务运维** \> **周期任务**。

5.  在周期任务页面，查看提交的同步任务详情。

    ![将MaxCompute数据同步到表格存储](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/6589997951/p52888.png)


