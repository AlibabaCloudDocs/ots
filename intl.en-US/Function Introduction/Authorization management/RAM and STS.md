# RAM and STS

The permission management mechanism of Alibaba Cloud includes Resource Access Management \(RAM\) and Security Token Service \(STS\). RAM user accounts with different permissions can be created to access Tablestore, and temporary access permission can also be granted to RAM users. RAM and STS greatly improve management flexibility and security.

RAM is used to control the permissions of each account. RAM allows you to manage permissions by granting different permissions to different RAM user accounts created under Alibaba Cloud accounts. For more information, see [RAM documentation](https://www.alibabacloud.com/help/doc-detail/28627.htm).

STS is a security credential \(token\) management system that grants users temporary access permission.

## Background

RAM and STS enable you to securely grant permissions to users without exposing your Alibaba Cloud account AccessKey pair. If the AccessKey pair of your Alibaba Cloud account is leaked, other users can operate on the resources under the account and access important information.

RAM allows you to manage permissions granted to RAM users on different entities and minimizes the adverse impact if the AccessKey pair of a RAM user is leaked. RAM user accounts are often used long term to perform&nbsp;operations. To ensure account confidential, the AccessKey pairs of RAM user accounts must be kept confidential.

In contrast to the permanent permission management function provided by RAM, STS provides temporary access authorization through a temporary AccessKey pair and token to allow temporary access to Tablestore. The permissions obtained from STS are restricted and are only valid for a limited period of time to minimize the adverse impact on the system in case of information leakage.

## Terms

The following table describes terms related to RAM and STS.

|Term|Description|
|:---|:----------|
|RAM user account|RAM user accounts are created under an Alibaba Cloud account and assigned independent passwords and permissions. Each RAM user account has an AccessKey pair. RAM user accounts can be used to perform authorized operations in the same way as the Alibaba Cloud account. In most cases, a RAM user account can be considered as a user with certain permissions or an operator with permissions for specific operations.|
|role|A role is a set of permissions that a user can assume. Roles do not have independent logon passwords and AccessKey pairs. RAM user accounts can assume roles. Permissions of a role are granted to RAM user accounts that assume the role.|
|policy|Policies are rules used to define permissions, such as the permissions to read from or write to certain resources.|
|resource|Resources are the cloud resources that users can access, such as individual Tablestore instances, all Tablestore instances, or a certain table in an instance.|

The relationship between a RAM user account and its roles is similar to a relationship between an individual and their social identities in different scenarios. For example, a person can assume the role of employee in a company and a role of parent at home. Different roles are assigned corresponding permissions. Roles are not actual users that can perform operations. Roles are complete only when being assumed by RAM user accounts. Furthermore, a role can be assumed by multiple users at the same time. The user who assumes a role is automatically assigned all permissions of the role.

Example:

Assume that an Alibaba Cloud account named Alice has two Tablestore instances named alice\_a and alice\_b. Alice has full permissions on both instances.

To maintain the security of the Alibaba Cloud account, Alice uses RAM to create two RAM user accounts: Bob and Carol. Bob has read and write permissions on alice\_a, and Carol has read and write permissions on alice\_b. Bob and Carol both have their own AccessKey pairs. If the AccessKey pair of Bob or Carol is leaked, only the corresponding instance is affected. Alice can then revoke the permissions of the compromised RAM user account through the console.

If Alice needs to authorize another RAM user to read the tables in alice\_a, instead of disclosing Bob's AccessKey pair to the user, Alice can create a new role such as AliceAReader and grant that role the read permission on alice\_a. However, AliceAReader cannot be used directly because it does not have a corresponding AccessKey pair.

To obtain temporary authorization, Alice can call AssumeRole to inform STS that the RAM user account Bob wants to assume the AliceAReader role. If AssumeRole is successfully called, STS returns a temporary AccessKey ID, AccessKey secret, and security token as access credentials. A temporary user assigned with these credentials is authorized to temporarily access alice\_a. The expiration time of the credentials is specified when AssumeRole is called.

## Design philosophy behind RAM and STS

RAM and STS are designed with complexity to achieve flexible access control at the cost of simplicity.

RAM user accounts and roles are separated to keep the entity that performs operations separating from the virtual entity that represents a group of permissions. Assume that a user requires multiple permissions such as read and write permissions, but each operation only requires one of the permissions. In this case, you can create two roles: one with the read permission and the other one with the write permission. Then you can create a RAM user account that does not have any permissions but can assume these roles. When the user needs to read or write data, the RAM user account can temporarily assume the role with the required permission. In addition, roles can be used to grant permissions to other Alibaba Cloud users, which makes collaborations easier and maintains strict account security.

Flexible access control does not mean that you have to use all these functions. You may only use a subset of functions as needed. For example, if you do not need to use temporary access credentials that have an expiration time, you can use only the RAM user account function.

The following topics provide examples to show how to use RAM and STS and suggestions on their usage. Operations in the examples are performed through the console and command lines to reduce the actual usage of code. If code must be used to perform such operations, see the [API Reference \(RAM\)](https://www.alibabacloud.com/help/doc-detail/28672.htm) and [API Reference \(STS\)](https://www.alibabacloud.com/help/doc-detail/28756.htm).

