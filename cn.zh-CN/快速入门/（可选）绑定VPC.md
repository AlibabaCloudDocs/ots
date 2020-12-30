# （可选）绑定VPC

将VPC绑定到表格存储实例后，VPC内的ECS实例可以通过VPC网络访问同地域的表格存储实例。

## 前提条件

已创建专有网络。具体操作，请参见[搭建IPv4专有网络](/cn.zh-CN/快速入门/搭建IPv4专有网络.md)或[搭建IPv6专有网络](/cn.zh-CN/快速入门/搭建IPv6专有网络.md)。

## 操作步骤

1.  登录[表格存储控制台](https://otsnext.console.aliyun.com/)。

2.  在概览页面，单击实例名称或在操作列单击**实例管理**。

3.  单击**网络管理**页签。

4.  在网络管理页签，单击**绑定VPC**。

    ![fig_tablestore_009](https://static-aliyun-doc.oss-accelerate.aliyuncs.com/assets/img/zh-CN/6117309951/p96310.png)

5.  选择目标VPC和交换机，输入**VPC Name**。

6.  单击**确定**。

    绑定成功后，在网络管理页签的**VPC列表**中查看已绑定的VPC信息。该VPC中的ECS实例可以通过VPC地址访问绑定的表格存储实例。

    单击已绑定的VPC信息对应的**解除绑定**，解除实例和VPC的绑定关系。解除绑定后，在该VPC内的ECS实例无法再通过VPC地址访问表格存储，如仍需访问请再次绑定。

    **说明：** 如果您用RAM用户登录管理VPC，请确保已使用主账号在[访问控制RAM - 用户管理](https://ram.console.aliyun.com/?spm=a2c4g.11186623.2.7.67ed198fFi9zEl#/user/list)授予该RAM用户相关的VPC权限（AliyunVPCReadOnlyAccess），否则会因为没有权限而无法获取相关的VPC信息。


