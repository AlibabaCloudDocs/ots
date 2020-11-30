# How does Tablestore use AccessKey pairs to control access?

Tablestore uses an AccessKey pair that consists of an AccessKey ID and AccessKey secret to authenticate the identity of a user. Users who pass the authentication can access all Tablestore resources owned by your account.

An account may have different AccessKey pairs. However, the Tablestore resources that you can access are the same when you use different AccessKey pairs.

To access Tablestore as a RAM user, the RAM user must be authorized by an Alibaba Cloud account to access specified resources.

