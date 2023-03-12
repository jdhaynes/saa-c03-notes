# 01 - AWS Accounts
* **AWS accounts** are a container for identities (users) and resources (things that are provisioned).
* Businesses may use multiple AWS accounts (e.g. test, dev, prod ) - better for security purposes to minimise damage if bad actor or accidentally errors involved.
* AWS accounts must have unique email address, but can share same credit card.

## Root Users
* Every AWS account has a unique root user.
* When creating a brand new AWS account, the root user is the only identity.
* Has full control over AWS account - cannot be restricted.

## Security
* Very important to secure the root user with MFA as it has full control.
* Better to utilise **Identity Access Management** users, groups and roles with limited permissions for security reasons.
* IAM identifies start off with no permission - explicitly must be granted.

## Billing and Usage
* Billing and usage alerts can be activated under Billing Dashboard -> Preferences -> Billing Preferences:
    * "Receive PDF invoice by email" - ticked
    * "Receive free tier usage alerts" - ticked
    * "Receive billing alerts" - ticked
* AWS budgets are good way to monitor costs to alert when certain thresholds are exceeded. Can be found in Billing Dashboard -> Cost Management -> Budgets.

## Identity & Access Management (IAM) Overview
* Functions of IAM:
    * Manage identities (users, groups and roles).
    * Authenticates - proves identity when making request.
    * Authorization - allows or denies access based on policies.
* IAM is globally resilient service - data is consistent across all AWS regions.
* IAM instance is seperate per AWS account and is is full trust between AWS account and IAM - has all permissions the root user has.
* AWS account has full trust of identities created by IAM.
* Best practice to only give minimal permissions required to perform a task - **least privileged access**.

### Types of Identity Obejcts
IAM can create 3 types of identity objects:
* User - humans or applications that require access to perform specific tasks.
* Group - collections of users which share same permissions.
* Role - used by AWS services or for granting access to external users - e.g. granting EC2 access to S3. Used when the number of users are uncertain (e.g. number of EC2 instances).

### Policies
* Policies are a set of rules that allow or deny access to certain AWS services.
* Only have effect when attached to identities (users, groups, roles).

### Access Keys
* A type of long term credential - dont change regularly or rotate.
* User is only IAM identity type that uses access keys.
* Used for programmatic access (e.g. API, SDKs, CLI).
* IAM users can have a maximum of 2 access keys.
* Two parts to an access keys:
    * Access key ID
    * Secret access key - can only be viewed once, note down!
* Can't change the secret access key - must delete and create new access key.
* Rotating access keys - why you can have 2 sets of access keys. Create a second, change the application to user the new keys, then delete the first - no downtime.

### Setting up AWS CLI
* Use **named profiles** to setup connections for each AWS account and IAM user - `aws configure --profile namehere`. Use `--profile` argument to each AWS command to use that profile.
* Setup profiles using access keys.
