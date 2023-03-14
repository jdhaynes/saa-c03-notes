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