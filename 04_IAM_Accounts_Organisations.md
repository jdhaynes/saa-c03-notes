# IAM, Accounts and AWS Organisations

## IAM Identity Policies
IAM identity policies are a set of security rules that ALLOW or DENY access to AWS services and can be attached to any IAM identity (user, group or role).

A **managed policy** is a policy that is reusable policy that can be applied to multiple users. It can be updated once and doesn't need to be updated for all users. An **inline policy** is one which is associated with a specific identity - good for applying special or exception permissions to specific users.

A statement in an IAM policy has the following components:
* `Sid` - optional field that acts as meaningful name to describe what it is doing
* `Effect` - allow or deny
* `Action` - the action to set the permission on. This is formatted as `service:operation`. This field is an array so multiple specific actions can be targetted, or wildcards can also be used to select multiple (e.g. `S3:*` targets all S3 actions).
* `Resource` - the AWS resource ARN to set the permission on. Also an array, so can target specific ARNs (e.g. `arn:aws:s3:::specificbucket`) and can also use wildcards.

Multiple policies can overlap, so it's possible for a user to be BOTH allowed and denied access at the same time. The following priority is applied:
1. **Explicit deny**: if any one of the relevant policies is a DENY, this takes priority and is not overruled by any other ALLOW.
2. **Explicit allow**: if there is a relevant ALLOW policy but no DENY, the user will be allowed access.
3. **Implicit deny**: by default, AWS denies access to everything unless an explicit allow is applied.

## IAM Users
IAM users are a type of IAM identity used where long-term access is required - e.g. humans and applications.

During **authentication**, a **principal** provides proof to AWS via credentials that it is the user that it is claiming to be. A principal is an entity that is making a request to AWS and hasn't yet been authenticated as a specific IAM user, and after authentication the principal becomes an **authenticated user**.

There is hard limit of 5000 IAM users per account, and an IAM user can be a member of max 10 IAM groups (also hard limit).

### AWS Resource Names (ARNs)
ARNs are IDs used to uniquely identity specific or groups of AWS resources. They have the format:

```
arn:partition:service:region:account-id:resource-id
arn:partition:service:region:account-id:resource-type/resource-id
arn:partition:service:region:account-id:resource-type:resource-id
```

* Always starts with `arn`.
* `partition` - always `aws` unless China (`aws-ch`).
* `service` - e.g. `s3`.
* `region` - not always applicable, so can put double colon instead (`::`).
* `account-id` - only needed for region-specific resources (e.g. EC2). Not required for global resources (e.g. S3).
* `resource-type` and `resource-id` changes based on the servic.e

Example of ARNs to set the `Action` of IAM identity policies:
* `arn:aws:s3:::catgifs` - refers to bucket-level actions on the `catgifs` bucket.
* `arn:aws:s3:::catgifs/*` - refers to object-level actions on objects within `catgifs`.

## IAM Groups
IAM groups are a collection of users that share the same IAM identity policies. They are used for easier management of groups of users which are related and share similar permissions.

Featues of IAM groups:
* Allow attachment of both inline and managed policies.
* Do not have credentials - you can't authenticate as a group.
* All users in a group inherit any attached policies.
* Hard limit of 5000 IAM users per group.
* Hard limit of 300 groups per AWS account.
* Groups cannot be nested.
* Groups are not a true identity and **cannot be referenced as a principal in another policy** such as by resource policies - e.g. an S3 bucket resource policy cannot grant access to a group.

## IAM Roles
IAM roles are short-lived identites that are used where there are multiple or an unknown number of principals, and so are useful for large groups of users (e.g. mobile app users interacting with an AWS resource). They are real identities and can be referenced in resource policies.

IAM roles are temporarily *assumed** by another identity (e.g. another IAM user, federated user). When a role is assumed, the `sts:AssumeRole` action in the Secure Token Service (STS) is used to generate a set of temporary time-limited credentials. 

IAM roles can have both inline and managed policies attached. Their are two types of policies:
* **Trust policies**: specifies what identities are allowed to assume the role.
* **Permission policies**: specifies what actions the role are allowed to perform.

### Example Use Cases of Roles
#### Emergency Situations
Give permissions to allow a team to do things they normally wouldn't do, but might need in an emergency situation. The team users can be given restricted permissions as part of their normal policies, but can assume the emergency role if they need to. This would be audited.

#### Identity Federation
Integrating AWS into an existing corperate environment may be an issue as there may be > 5000 users (hard limit of IAM users), or they may use SSO (cannot access AWS directly). Good solution is to create an IAM role that these external identities can assume - can be achieved through identity federation.

#### Apps with Large Number of Users
Allow large number of users to access AWS resources through existing web identities (e.g. Google, Facebook). Use web identity federation to trust these web identities and allow them to assume role with the required permissions. Useful as no credentials need to be stored in the app.

#### Cross-Account Access
Allows one account to grant access to another account by assuming a role, without having to create new users for them.

## AWS Organisations
AWS organisations are a way of grouping together multiple AWS accounts so they can be managed more easily through a central service, rather than individually.

Features of AWS organisations:
* Organisations have a tree structure, consisting of the **organisation root** which can contain zero **member accounts** underneath it. 
* Organisations have a single **management account** (a.k.a. master account or billing account).
* Accounts can be grouped into **organisational units** (OUs), which group multiple accounts together to administer as a unit. OUs can be nested, creating a hierarchy. 
* Organisations enable consolidated billing - payments details of the management account are passed down, and billing is consolidated from all member accounts into single place.
* Reservation discounts and volume discounts of member accounts are consolidated - cost efficient!

### Creating Organisations
* Can attach existing accounts - sends invite to account that must accept.
* Can create new account directly in organisation - still needs unique email.

### Use of Roles in Organisations
It is best practice to create a single account used for login, and then allow those identities to **role switch** into roles so they can access other accounts. Some organisations may use a seperate AWS account just for setting up identifies, whilst others may have this in the management account.

When creating a new account **directly** in the account, AWS creates an admin role called `OrganizationAccountAccessRole` that trusts the management account. This enables the management account to role switch into the new account without logging into it directly.

When attaching existing accounts, the `OrganizationAccountAccessRole` must be created manually in the attached account by giving it admin permissions. Note - it doesn't need to be named this, but is the convention.

### Service Control Policies (SCPs)
SCPs are a way to controlling what an account can and can't do - e.g. provisioning certain resources. SCPs are specified in a JSON policy which can be attached to either:
* The root container - applies to whole organisation
* An organisational unit (OU) - applies to all accounts within the OU
* An individual member account

SCPs **cannot restrict the management account**, so it is best practice to keep the management account as clean as possible and not use it to provision resources.

SCPs limit what all identities inside an account can do. This includes the root, although it doesn't do this directly as nothing can restrict the root - rather SCPs restrict what an account can do, which indirectly affects what the root can do at full permissions.

As SCP has a default implict deny for everything, the default action by AWS when enabling SCP is to add a `FullAWSAccess` SCP that enables everything (i.e. same effect as having SCP disabled):

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "*",
    "Resource": "*"
  }
}
```

There are two approaches to implementing SCP:
* Deny lists
* Allow lists

#### Deny Lists
Deny lists work build on top of the default `FullAWSAccess` SCP by denying specific services - all other ones are allowed. For example, the following SCP added in addition to default `FullAWSAccess` SCP will allow access to all operations but S3.

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Deny",
    "Action": "s3:*",
    "Resource": "*"
  }
}
```

Deny lists are useful as you don't need to change the SCPs when new services are added - less admin overhead.

#### Allow Lists
Allow lists only explicitly allow services specified in the SCP. This requires the `FullAWSAccess` SCP to be removed first so that everything is explicitly denied, then the new SCP to be added that allows only the services required.

This SCP allows only S3 and EC2:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
        "Effect": "Allow",
        "Action": [
            "s3:*",
            "ec2:*"
        ],
    "Resource": "*"
    }
  ]
}
```
