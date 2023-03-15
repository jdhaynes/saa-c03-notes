# AWS Fundamentals

## Public vs Private Services
There are three network zones, which refer to networking capablity rather than permissions.

* **Public internet zone**: allows users to access public AWS service via internet networking.
* **Public AWS zone**: attached to the public internet zone, and allows users on the internet to connect to AWS services (e.g. S3 buckets). Public refers to ability that users on the public internet can connect to a service, but doesn't imply that they can actually access it due to permissions.
* **Private AWS zone**: services that exist within a **Virtual Private Cloud (VPC)**. They are not accessable in the public AWS zone unless configured.

## AWS Global Infrastructure
### AWS Regions
An **AWS region** is a geographic location which has a full deployment of AWS instructure - e.g. N. Virginia, Ohio, Sydney, London. Most AWS services are associated with a specific AWS region (e.g. E2), but some are global (e.g. IAM).

Benefits:
* Isolated fault domain - regions are completely isolated from eachother geographically, so problems in one region won't affect others.
* Geopolitical and governance seperation - each region is subject to different data governance. Data does not move between regions unless setup.
* Location control - tune for performance by moving infrastructure closer to customers.

### Availibility Zones (AZ)
Availability zones are seperated infrastructure within an AWS region - they are isolated from eachother in terms of storage, compute, networking and physical location. If one AZ experiences a power outage, the other AZs remain functional.

Can increase resilience of solution by distributing instances across multiple AZs within a region using virtual private clouds (VPCs) that span AZs.

### AWS Edge Locations
As AWS regions are not always near customers, **AWS edge locations** act as local distribution points at far more locations than AWS regions - this helps with reducing latency by reducing the distance between the client and the server. Example - Netflix serving video content as close to customers as possible with low latency.

### Levels of AWS Service Resilience
There are three different levels of resilience to describe an AWS service:
* Globally resilient - data is replicated globally across multiple regions, so isn't associated with a specific region. Can tolerate multiple failures. Examples are Route 53 and IAM.
* Region resilient - replicated across multiple AZs within a region. Continues operating if an AZ fails, but will fail if the whole region fails.
* AZ resilient - run only from single AZ, so the service will fail if the AZ fails. Prone to failure.

## Virtual Private Clouds (VPCs)
* VPCs are private networks for private AWS services to run on. Also can be used to connect to on-premises networks (in hybrid environment) or to other cloud providers.
* Within 1 account and 1 AWS region.
* Private and isolated from other VPCs or public AWS zone (unless configured otherwise).
* Regionally resilient - distributed across multiple AZs in a region.
* **VPC CIDR** - the range of IP addresses that are allocated for services within the VPC to use.
* Other components are created along with the default VPC:
    * Internet gateway
    * Network ACL
    * Security group

Two types:
* Default VPC
    * Initially created by AWS and pre-configured in a specific, predictable away 
    * Less flexible than custom VPCs.
    * Can only have one per region.
    * Configured to have only one VPC CIDR range - is always 172.31.0.0/16
    * The /16 CIDR is split up into multiple /20 subnets, one for each AZ in the region. This makes the VPC regionally resilient. Note - there is a maximum of 16x /20 subnets within /16, so maximum of 16x AZs.
    * Can delete - you don't need it and not always suited for production due to flexibility.
* Custom VPC - 
    * Can have **many** per region. 
    * Completely configurable.


## Elastic Cloud Compute (EC2)
EC2 provides virtual machine instances as infrastructure-as-a-service (IAAS). 
* Use VPC networking and are private by default, but can be exposed to public. 
* AZ resilient - any given EC2 instance is associated with one AZ.
* Come in may sizes with different hardware allocations.
* Billed on demand (by second or hour). 
* Storage either provided by local on-host storage, or Elastic Block Store (EBS)
* A **key pair** must be created and downloaded locally to be able to connect to an EC2 instance via SSH.

### Instance Lifecycle
EC2 instances have a **state** that indicates the status of the instance in the lifecycle (e.g. running, stopped or terminated). The state determines what the customer is billed for.

| State -> | Running | Stopped | Terminated |
| - | - | - | - |
| Compute | Billable |  |  |
| Memory | Billable |  |  |
| Storage | Billable | Billable |  |
| Networking | Billable |  |  |

### Amazon Machine Images (AMIs)
AMIs are snapshots of an operating system that can be used to launch new EC2 instances. AMIs can also be created from existing EC2 instances. AMIs have permissions on who can use it (e.g. public, or explicit access of specific users).