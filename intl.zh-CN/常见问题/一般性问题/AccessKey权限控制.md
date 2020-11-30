# AccessKey权限控制

表格存储目前支持通过AccessKey ID和AccessKey Secret对用户身份进行认证，认证通过的用户对表格存储内的资源访问不受限。

一个用户的帐户下可以有多组不同的AccessKey ID和AccessKey Secret对，同一个帐户使用不同AccessKey ID和AccessKey Secret看到的表格存储资源是相同的。

如果您使用RAM用户，则RAM用户需要被主账号授予权限，且RAM用户只能访问被授权的资源。配置RAM用户权限的具体操作，请参见[配置用户权限]()。

