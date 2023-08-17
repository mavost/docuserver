# Working with Amazon Web Services (AWS)

## Adding AWS-CLI to Ubuntu (v. 20.04)

### Installation

- `curl -sS "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-2.0.30.zip" -o "awscliv2.zip"`  
  update above path by looking up [latest-version](https://github.com/aws/aws-cli/blob/v2/CHANGELOG.rst)
- unzip content and install `unzip awscliv2.zip && sudo ./aws/install`
- verfiy installation and cleanup `aws --version && rm -rf aws && rm awscliv2.zip`

### Linking the CLI to your AWS account

- check "programatic access" in IAM user detail
- add new user
- add permissions - policies(left) - "AdministratorAccess"
- open User summary &rightarrow; "Security credentials" &downarrow; "Access keys" &rightarrow; "Create access key" download CSV or copy/paste key to a safe place

  ``` {bash}
  Example:
  AKIA3RN7------RVROB5
  gzpYwF9M-------------GhsODuvpXaa1JtRuhVY
  ```

- add a profile to the CLI by running `aws configure --profile awscliuser`  
  &rightarrow; type in keys  
  &rightarrow; type in default region "eu-central-1"  
  &rightarrow; type in default output "json"

- credential files (config, credentials) are located in `$HOME\.aws`
- testing access to an existing resource in your AWS account, e.g. by `aws s3 ls --profile awscliuser` replies

  ``` {bash}
  2021-08-05 11:58:32 inventory-12345
  2021-09-01 15:04:55 example.com
  2021-09-01 18:47:09 www.example.com
  ```

- by adding the parameter `export AWS_PROFILE=awscliuser` in either .profile or .bashrc you can omit the "--profile awscliuser"
identification when submitting commands interactively

### Adding bash shell syntax completion

Edit *.bashrc* file by adding the following line and executing the file:  
`[ -x /usr/local/bin/aws_completer ] && complete -C '/usr/local/bin/aws_completer' aws`  
which triggers given that the **aws_completer** command is present

### (optional) Installing AWS serverless application model (SAM) CLI

[source](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install-linux.html)

- `curl -sSLO "https://github.com/aws/aws-sam-cli/releases/download/v1.27.0/aws-sam-cli-linux-x86_64.zip"`  
  Note: had problems with the master version post(1.27) not deploying the bootstrap system
- unzip content and install `unzip aws-sam-cli-linux-x86_64.zip -d sam-cli && sudo sam-cli/install`
- verify installation and cleanup `/usr/local/bin/sam --version && rm -rf sam-cli && rm aws-sam-cli-linux-x86_64.zip`  
  Note: `/usr/local/bin` should actually be included in `PATH` variable already

#### Working with AWS SAM CLI

1. `sam init` initializes a serverless application with an AWS SAM template in the [language of choice](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-cli-command-reference-sam-init.html)
2. to build your serverless application, use the `sam build` [command](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-using-build.html)
3. deploy your application using the `sam deploy --guided --debug` [command](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-deploying.html)  
  <img src="./images/2021-09-04_AWS-SAM-deploy.png" alt="AWS SAM output" width="720"/>
4. Testing serverless Lambda function  
  `curl https://----API-----.execute-api.eu-central-1.amazonaws.com/Prod/hello/ -w "\n"`  
  with `----API-----` referring to the endpoint-URL generated in the previous step replies in JSON format:  
  `{"message":"hello world"}`

---

## Hosting a static website on AWS S3 buckets

Sequence:

1. setting up *S3 bucket* with *bucketname* (e.g. *mavost.ml*), access permissions and inserting content
    - unblock **all public access** by deselecting check box and confirming decision
    - add access policy with customized *bucketname*

      ``` json
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
              "s3:GetObject"
            ],
            "Resource": [
              "arn:aws:s3:::bucketname/*"
            ]
          }
        ]
      }
      ```

    - initial upload of files, e.g. from your own repository (Add Files / Folders)
    - added a second bucket called *www.bucketname* and repeated above first two steps but instead
    of uploading the same hosted content again I added a redirect  
    <img src="./images/2021-09-01_AWS_S3_subdomain_redirect.png" alt="Adjusting redirect of one subdomain to another" width="728"/>

2. setting up *Route 53* by adding a hosted zone
    - add new hosted zone (not included in AWS free tier!) for both bucket destinations  
      domain *bucketname* &rightarrow; s3-website.eu-central-1.amazonaws.com S3 bucket  
      subdomain *www.bucketname* &rightarrow; s3-website.eu-central-1.amazonaws.com S3 bucket
    - screenshot  
      <img src="./images/2021-09-01_AWS_Route53_HostedZoneS3.png" alt="Setting up Route53 hosted zone for website S3 bucket" width="728"/>
    - copy AWS Route 53 DNS server names for use in step 3

3. add server names in Freenom Domain administration
    - screenshot  
      <img src="./images/2021-09-01_freenom_DNS.png" alt="Entering AWS Route 53 DNS servers to Freenom Domain Settings" height="410"/>

4. setting up AWS CodePipeline (one pipeline covered by FreeTier)
    - screenshot  
      <img src="./images/2021-09-01_AWS_CodePipeline_GitHub-S3.png" alt="Setup for AWS CodePipeline" width="728"/>

---

### Advanced web hosting of the S3 content using Cloudfront, Route 53 and a Freenom domain

- Fine-tuning: In the Cloudfront distribution, for the origin-URL, instead of using the S3 bucket endpoint, use the S3 static website url which is slightly different. Distributions then are all working with subdirectory "/" endpoints going directly to the *index.html* files within the subdirectory.

- Note: the Cloudfront cache does only update once a day so your hosted site will refresh much slower than what you expect, [source](https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-serving-outdated-content-s3/)  
&rightarrow; use the s3 website endpoint to verify the result  
&rightarrow; add versioning or flush the cache (*expect added costs*)

---

## AWS Certified Solutions Architect - Associate Course Preparation

### Service capabilities copy/pasted from exams' answers

#### Amazon Inspector

Amazon Inspector is a vulnerability management service that continuously scans your AWS workloads for vulnerabilities. It would not be useful to get a broad overview of things like cost optimization, performance, security, fault tolerance, or service limits.

#### AWS Trusted Advisor

AWS Trusted Advisor provides recommendations by using checks that help you follow AWS best practices. Trusted Advisor evaluates your account by using checks that identify ways to optimize your AWS infrastructure, improve security and performance, reduce costs, and monitor service quotas.

#### AWS Firewall Manager

AWS Firewall Manager is a security management service that allows you to centrally configure and manage firewall rules across your accounts and applications in AWS Organizations. It cannot filter network traffic before it reaches your internet gateway.  
With AWS Network Firewall, you can define firewall rules that provide fine-grained control over network traffic.  
Reference: [AWS Network Firewall](https://aws.amazon.com/de/firewall-manager/)

#### AWS Network Firewall

With AWS Network Firewall, you can define firewall rules that provide fine-grained control over network traffic.  
Reference: [AWS Network Firewall](https://aws.amazon.com/de/network-firewall/)

#### AWS Artifact

AWS Artifact is your go-to, central resource for compliance-related information that matters to you. It provides on-demand access to security and compliance reports from AWS and Independent Software Vendors (ISVs) who sell their products on AWS Marketplace.  
Reference: [AWS Artifact](https://aws.amazon.com/de/artifact/)

#### AWS Audit Manager

AWS Audit Manager to map your compliance requirements to AWS usage data with prebuilt and custom frameworks and automated evidence collection. It is not used to provide compliance reports.  
Reference: [AWS Audit Manager](https://aws.amazon.com/de/audit-manager/)

#### Amazon Macie

Amazon Macie is a data security and data privacy service that uses machine learning (ML) and pattern matching to discover and protect your sensitive data.  
Reference: [Amazon Macie](https://aws.amazon.com/de/macie/)

#### AWS Shield

AWS Shield is a managed DDoS protection service that safeguards applications running on AWS.
AWS Shield does not provide cost protection.  
Reference: [AWS Shield](https://aws.amazon.com/de/shield/)

#### CloudTrail

AWS CloudTrail is a web service that records activity made on your account and delivers log files to your Amazon S3 bucket. CloudTrail provides visibility into user activity by recording actions taken on your account. CloudTrail records important information about each action, including who made the request, the services used, the actions performed, parameters for the actions, and the response elements returned by the AWS service. This information helps you track changes made to your AWS resources and to troubleshoot operational issues.    CloudTrail makes it easier to ensure compliance with internal policies and regulatory standards.  
Reference: [CloudTrail](https://aws.amazon.com/cloudtrail/faqs/)

#### CloudWatch

Create a billing alarm in CloudWatch for the specified amount.
This would be the most cost-effective and efficient way of alerting someone when their AWS bill reaches a certain threshold.

#### Amazon GuardDuty

Amazon GuardDuty is a threat detection service that continuously monitors your AWS accounts and workloads for malicious activity and delivers detailed security findings for visibility and remediation.  
Reference: [Amazon GuardDuty](https://aws.amazon.com/de/guardduty/)

#### Amazon Detective

- Amazon Detective simplifies the investigative process and helps security teams conduct faster and more effective investigations. With the Amazon Detective prebuilt data aggregations, summaries, and context, you can quickly analyze and determine the nature and extent of possible security issues.

- Uses AI/ML

Reference: [Amazon Detective](https://aws.amazon.com/de/detective/)

#### AWS WAF

WAF is a web application firewall that helps protect your web applications or APIs against common web exploits that may affect availability, compromise security, or consume excessive resources. AWS WAF gives you control over how traffic reaches your applications by enabling you to create security rules that block common attack patterns, such as SQL injection or cross-site scripting, and rules that filter out specific traffic patterns you define. You can get started quickly using Managed Rules for AWS WAF, a pre-configured set of rules managed by AWS or AWS Marketplace Sellers. The Managed Rules for WAF address issues like the OWASP Top 10 security risks. These rules are regularly updated as new issues emerge. AWS WAF includes a full-featured API that you can use to automate the creation, deployment, and maintenance of security rules.  
Reference: [AWS WAF](https://aws.amazon.com/waf/)

#### Bastion Host

Create the bastion host (EC2 instance) in a public subnet. For the instance security group, add ingress on port 22, and specify the address range of the personnel in the data center. Use a private key to connect to the bastion host. Add an internet gateway, a route table, and a route to the internet gateway in the route table.
Including bastion hosts in your VPC environment enables you to securely connect to your Linux instances without exposing your environment to the internet. After you set up your bastion hosts, you can access the other instances in your VPC through Secure Shell (SSH) connections on Linux. Bastion hosts are also configured with security groups to provide fine-grained ingress control. An internet gateway enables resources in your public subnets to connect to the internet.

#### DB Failover

Failover is automatically handled by Amazon RDS so that you can resume database operations as quickly as possible without administrative intervention. When failing over, Amazon RDS simply flips the canonical name record (CNAME) for your DB instance to point at the standby, which is in turn promoted to become the new primary. We encourage you to follow best practices and implement database connection retry at the application layer.
Failovers, as defined by the interval between the detection of the failure on the primary and the resumption of transactions on the standby, typically complete within one to two minutes. Failover time can also be affected by whether large uncommitted transactions must be recovered; the use of adequately large instance types is recommended with Multi-AZ for best results. AWS also recommends the use of Provisioned IOPS with Multi-AZ instances for fast, predictable, and consistent throughput performance.  
Reference: [AWS RDS](https://aws.amazon.com/rds/faqs/)

#### Simple Workflow Queue

Does not guarantee FIFO and that no messages were lost or processed twice.

#### Amazon Kinesis Data Stream

Performant message queue without data loss, duplication, FIFO

#### Simple Queue Service (SQS)

Standard: Does not guarantee FIFO and that no messages were lost or processed twice.
SQS-FIFO: FIFO (First-In-First-Out) queues are designed to enhance messaging between applications when the order of operations and events is critical or where duplicates can't be tolerated.
SQS LIFO queue:
SQS dead-letter queue: An SQS dead-letter queue is where other SQS queues (source queues) can send messages that can't be processed (consumed) successfully. It's great for debugging as it allows you to isolate issues so you can debug why their processing doesn't succeed.

#### AWS Storage Gateway - File Gateway

File Gateway supports NFS and SMB protocol and can integrate with an on-premises Active Directory.

#### AWS Storage Gateway - Tape Gateway

This solution is used mainly for backups. AWS Storage Gateway is not the most cost-effective method of migrating data to the cloud.

#### AWS Snowball

AWS Snowball is the most cost-effective method of migrating data to the cloud in the order of terabytes.

#### AWS DataSync

You work for an advertising company that has a large amount of data hosted on-premises. They would like to move this data to AWS and then decommission the on-premises data center. What would be the easiest way to achieve this?This is the best answer because you are decommissioning the on-premises infrastructure, so you should use AWS DataSync to migrate the data to AWS.

#### AWS SMS (Server Migration Service)

to incrementally perform migrations of all VMs in the data center to AWS as AMIs for Amazon EC2. AWS SMS helps minimize downtime due to the incremental nature of the migrations. You can use it to easily migrate existing virtual machines from vCenter to AWS Amazon EC2 instances.  
Reference: [AWS SMS (deprecated?)](https://www.amazonaws.cn/en/server-migration-service/faqs/)

#### AWS MGN (Application Migration Service)

AWS MGN is a service meant to simplify and optimize the lift-and-shift process for migrating existing on-premises infrastructure to the AWS cloud. It will automatically convert and launch your servers into AWS, so you can take advantage of all of the AWS benefits.  
Reference: [AWS MGN](https://aws.amazon.com/application-migration-service/)

#### AWS DMS (Database Migration Service)

AWS DMS is a database migration service that is meant for only migrating database instances. It makes it easy to migrate your relational databases, data warehouses, NoSQL databases, and other data stores to or from the AWS cloud. You can do ongoing replications or just one-time migrations.
AWS DMS offers the ability to enable Change Data Capture during migrations, which allows replication of ongoing data changes from the source to your target data store. This allows you to ensure all data is synced post migration.  
Reference: [AWS DMS](https://aws.amazon.com/dms/)

#### AWS Migration Hub

AWS Migration Hub does not perform migrations as it is meant to provide a single place to view and track existing and new migration efforts between other AWS services.

#### AWS Wavelength

You work for a mobile telecommunications company that is looking to partner with a popular electric car company. The partnership will allow people to design applications for their vehicles that will use a 5G network connectivity to store vehicle location information, temperature status, and other diagnostic data on the AWS Cloud; however, it will cache the information at an edge location provided by the cellular company first, so as to maximize efficiency. Which AWS service could you use to create this? AWS Wavelength embeds AWS compute and storage services within 5G networks and would be the best service to use in this scenario, i.e., ultra-low latency using 5G to AWS resources.

#### AWS RDS

Workhorse for OLTP and supports SQL Server, MySQL, MariaDB, PostgreSQL, Oracle on top of Aurora

Multi-AZ: handles replication of primary DB to standby DBs for increased availability; mandatorily included in Aurora instances; an RDS will automatically failover to the secondary instance on primary failing. Can not be used for performance increase. Only one DNS entry.

Read-Replica: Read-only copy of primary R/W DB for increased scalability; can be cross-AZ and also cross-region, not directly intended for disaster recovery. Both instances have their own DNS entry one can point to. You can use this configuration for disaster recovery with adjustments. Requires RDS option "Automatic Backups" enabled to work. up to five read replicas per instance.

#### AWS Aurora

RDBS developed by AWS which is compatible to MySQL and PostgreSQL and significantly faster than both. Scales in 10GB increments up to 128TB.
Features:

- Strong data replication: 2 * 3 AZ copies will be generated.
- Self-healing capabilities as inconsistencies are actively sought and corrected
- Read/write performance only impacted after losing 3/2 copies, i.e. replicas.
- Replicas can be Aurora(15)/MySQL(5)/PostgreSQL(5) flavor
- Automated backups enabled at default price
- offers a serverless flavor for spiky/unpredictable loads

#### Aurora Serverless

Aurora Serverless automatically starts up, shuts down, and scales capacity up or down based on your application's needs, and you pay only for capacity consumed.

#### AWS DynamoDB

DynamoDB is a fully managed NoSQL database service that provides fast and predictable performance and scalability. Stored on SSD. Single-digit millisecond latency.

Features:

- automatic replication across three AZs
- needs a gateway to be accessed from a VPC
- defaults to eventually consistent reads (wait 1s) and optionally offers strongly consistent reads at higher latency but better reliability
- offers caching service DynamoDB Accelerator (DAX) which can be put between Application and DynamoDB to even further speed things up
- pay per request which is generally more expensive than provisioned capacity
- offers encryption using KMS
- integrates with CloudWatch/CloudTrail/DirectConnect(DX)/IAM policies and roles/VPC endpoints/VPN connections
- offers *DynamoDB transactions* to force ACID compliance on a table (atomicity, consistency, isolation, durability) in a "all or nothing" way
- offers *DynamoDB Backup* to save/restore data without performance impact in a particular Region
- offers "Point-in-Time Recovery* to protect against accidental writes/deletes, i.e. rolling back the DB to a previous state (between 5min to last 35d)
- offers *Streams* as a time-ordered sequence of item-level changes in a table for the last 24 hours
- offers *Global tables*, i.e., multi-master/multi-Region replication, as part of the DynamoDB streams functionality (at latency <1s)

#### Amazon DocumentDB

Offers a service to run MongoDB workloads without having to worry about maintenance.

Use case:
On-Prem MongoDB server &rightarrow; AWS DB Migration Service &rightarrow; DocumentDB

#### Amazon Keyspaces

Offers a service to run Cassandra NoSQL DB without having to worry about maintenance and saving cost as it is serverless.

#### Amazon Neptune

Amazon's Graph DB service. Used for entity resolution, knowledge graphs, fraud detection.

#### Amazon Quantum Ledger Database (QLDB) for Ledger DBs

Blockchain, financial, logistics, supply-chain transactional ops.  Immutable, transparent, crypto,  blah, blah...

#### Amazon Timestream for Time-Series Data

This would be the best and most cost-effective solution for storing time-series data.

Use cases:

- IoT: agriculture
- Analytics:
- DevOps:

#### AWS Redshift

Workhorse for OLAP

#### Autoscaling Group

Use a predictive scaling policy on the Auto Scaling Group to meet opening and closing spikes.

Using data collected from your actual EC2 usage and further informed by billions of data points drawn from our own observations, we use well-trained Machine Learning models to predict your expected traffic (and EC2 usage) including daily and weekly patterns. The model needs at least one day’s of historical data to start making predictions; it is re-evaluated every 24 hours to create a forecast for the next 48 hours.

What we can gather from the question is that the spikes at the beginning and end of day can potentially affect performance. Sure, we can use dynamic scaling, but remember, scaling up takes a little bit of time. We have the information to be proactive, use predictive scaling, and be ready for these spikes at opening and closing. If scale by schedule was an option here, it would be a GREAT option. On your AWS exam, you won't always be given the option of the most correct solution.

#### VPC

Cooking recipe:

1. Manually creating a VPC results in the following objects being generated, automatically:

    - Default Security Group
    - Network Access Control List (NACL)
    - Main Route Table  

2. Adding representations of public/private subnets: CIDR Address ranges for VPC/subnets can go from 0.0.0.0/16 to 0.0.0.0/28 and IP address range for each subnet is reduced by 5 addresses reserved by AWS. Subnets can not span multiple AZs.
3. Enabling auto-assignment of public IPs in public subnets
4. Create internet gateway and attach it to the VPC - only one IG per VPC.
5. **Best practice**: Create individual route table for public subnets. Create route from IG to the internet: `0.0.0.0/0`. Associate public subnets. (in this way any new subnets will only inherit settings from the main route table which are more restrictive usually)
6. Deploy instances, a web server (public subnet) and a DB server (private subnet). :-)
7. Add a SG for **public internet** inbound web traffic (HTTP/S, SSH) and associate it with the web server. Add another SG for inbound DB connectivity (ICMP/ping, HTTP(dbadmin), SSH and MYSQL) from all **public subnets** in the VPC.
8. Ping/SSH into the other machine from the web server and note that the DB server is disconnected from the internet.

##### NAT Gateway

Used to enable instances to communicate with the internet. It is placed as an quasi-instance in a public subnet, effectively providing redundant internet access, there. No SGs involved. No need to patch.

1. Create NAT gateway, assign elastic IP address.
2. Edit main route table by adding any subnet IP ranges pointing to the NAT gateway.

##### Network ACL (NACL)

First line of defense against intruders in form of a firewall between route tables and subnets:

- The default NACL coming with a VPC allows all traffic
- Newly created NACLs block all traffic
- Each subnet only can have association with on NACL
- NACL can associate with many subnets
- NACLs are stateless
- NACLs have separate inbound/outbound rules
- NACL firewall rules are executed in an ordered list from lowest to highest number

For a new network ACL for internet-facing purposes:

1. allow required inbound traffic rules
2. allow required outbound traffic rules
3. add ephemeral port ranges to outbound traffic rules, e.g. for NAT.

##### VPC Endpoints

continue here

#### AWS Outposts rack

AWS Outposts rack is a fully managed service that extends AWS infrastructure, services, APIs, and tools on-premises and would be suitable here.

#### AWS Well-Architected (WA) Tool

The AWS WA Tool offers a means of consistently measuring your AWS architectures against standardized best practices. It helps assist in documenting decisions, providing recommendations, and guiding you in design decisions.

#### AWS IAM

- Provide access and identity management for all regions of an account
- Connect entities (groups, users, roles) with permissions on resources (policies)
- Users can and should inherit permissions from groups
- The *Principle of Least Privilege* states to only provide a user with the minimum amount of permissions necessary to do their job in the cloud
- Roles are temporary means for a user/machine/resource to switch to another set of permissions **even on different accounts**

##### Best Practices

- When provisioning resources with a use case in mind which involves interaction with other services one should assign roles directly to the acting resource. In this way one does not have to involve user generation.

1. Create a role with required secondary service policies
2. Configure machine by assigning the role and downloading PEM credential key
3. Copy IP address of launched instance
4. Login to machine via ssh or direct connection in management console

    ```bash
    cd Downloads
    # close access to other users
    chmod 400 MACHINECREDENTIAL.pem
    # enter machine and accept its ED25519 key fingerprint
    ssh ec2-user@MACHINEIP -i MACHINECREDENTIAL.pem
    # update machine
    sudo su
    yum update -y
    # leave super user mode
    exit
    ```

5. Given that machine IAM role includes S3 admin access:

    ```bash
    # list buckets in account
    aws s3 ls
    # create bucket
    aws s3 mb training_dummy_1020202020
    # create file
    echo "hello, this is my dummy file" > hello.txt
    # upload file
    aws s3 cp hello.txt s3://training_dummy_1020202020
    # list files in bucket
    aws s3 ls s3://training_dummy_1020202020
    ```

- Instead of the above, one could configure the aws CLI by running `aws configure` with a user's access key and secret access key, given the user carries the required permissions/policies, effectively hard-coding your security into the machine &rightarrow; not recommended
- Roles in contrast can be altered and reassigned without disturbing a running instance to which they are attached.

#### AWS EC2 - Elastic Compute Cloud

##### Pricing Tiers

- "On Demand" for flexible use, PoCs, unknown long term use
- "Reserved", renting machines for minimum 1 year (or optional 3 years) at a discount up to 72%, for constantly required workloads, e.g. dynamic web servers
- "Reserved Flex": as above with option to switch to similar/higher machine type at up to 50% savings rate, scaling up/across is possible
- "Spot": unused capacity at fluctuating, but massively reduced prices (up to 90%) for non-time critical workloads
- "Dedicated": running workloads on a physically isolated hardware/rack to fulfil legal, regulatory, licensing requirements at increased cost over all the other tiers where accounts might share the same physical hardware, isolated by virtualization software. Can be booked "On Demand"/"Reserved".

##### AMIs

- Linux2 AMI: billed in seconds
- Windows: billed in minutes

##### Compute Families / Sizes

##### Placement Groups

Placement groups organize instances of similar purpose an can boost performance, durability/resilience of an architecture.

- Only certain type offer to be put in a placement group
- You can move/reorganize them from the CLI/SDK, only, and they need to be stopped restarted
- You can't merge them
- AWS recommends homogeneous instances in a PG
- A Cluster PG can't span multiple AZs, while the others can.

1. Cluster PG: performance, low latency, high network throughput
2. Spread PG: instances are placed on distinct underlying hardware; used for individual critical instances
3. Partition PG: individual racks (network/power source) for each partition

##### Networking Interfaces

- ENI (Elastic Network Interface): all purpose, low throughput, used to separate networking according to purposes.
- EN (Enhanced Networking): for speeds between 10 - 100 Gbps. Always prefer the newer ENA (Elastic Network Adapter) over the Intel 82599 VF (Virtual Function) option. SR-IOV (single root IO virtualization) puts less stress on instance CPU.
- EFA (Elastic Fabric Adapter): High performance computing and ML use cases; offers further speedup by OS-bypass technology

ENI < EN-VF < ENA < EFA

##### Communication with Instances

- Linux, SSH, port 22
- Windows, RDP, port 3389
- Browser, HTTP, port 80
- Browser-encrypted, HTTPS, port 443

##### Security groups

Security groups are virtual firewalls for resources in your VPC, e.g., EC2 instances.

- Basically, all inbound communication is blocked by default. To let "everything" in on a certain port/protocol allow IPv4 0.0.0.0/0 and IPv6 ::/0 address ranges.
- All outbound traffic is allowed by default.
- Changes to security groups take effect immediately.
- You can have several EC2 instance within the same SG.
- You can have several SGs attached to an EC2 instance.

The following are the basic characteristics of security groups for your VPC:

- SCs act at the instance level, not the subnet level.
- There are quotas on the number of security groups that you can create per VPC, the number of rules that you can add to each security group, and the number of security groups that you can associate with a network interface. For more information, see Amazon VPC quotas.
- You can specify allow rules, but not deny rules.
- You can specify separate rules for inbound and outbound traffic.
When you create a security group, it has no inbound rules. Therefore, no inbound traffic originating from another host to your instance is allowed until you add inbound rules to the security group.
By default, a security group includes an outbound rule that allows all outbound traffic. You can remove the rule and add outbound rules that allow specific outbound traffic only. If your security group has no outbound rules, no outbound traffic originating from your instance is allowed.
- Security groups are stateful. If you send a request from your instance, the response traffic for that request is allowed to flow in regardless of inbound security group rules. Responses to allowed inbound traffic are allowed to flow out, regardless of outbound rules.  

Reference: [Security Groups](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_SecurityGroups.html)

##### Bootstrap script

It runs during the first launch of an instance at root user permission.  
Example:

```bash
#!/bin/bash
yum update -y
# install web server
yum install httpd -y
# start web server
yum service httpd start
# provide content
echo "<html><body><h1>Hello World!</h1></body></html>" > /var/www/html/index.html
```

##### Meta Data

Can be queried from within an EC2 instance, e.g., during a bootstrap script:

```bash
echo "<html><body><h1>Hello World! My IP is " > /var/www/html/index.html
curl http://169.254.169.254/latest/meta-data/public-ipv4 >> /var/www/html/index.html
echo "</h1></body></html>" >> /var/www/html/index.html
```

User data within an EC2 instance can be queried using `http://169.254.169.254/latest/user-data` and provides the bootstrap script.

#### S3

Data in object store is cached/replicated by AWS to reach 11 x 9's Durability and mostly 99.99% availability.

The entire bucket namespace is stretching across all AWS accounts, i.e. it is global and bucket names must be unique. However, the region chosen for a bucket is still fixed on a particular location.  
Object URLs map to https://#bucket#.s3.amazonaws.com/#prefix#/#key#.  
Successful object uploads result in a HTTP 200 code.

##### Storage classes

- S3 Standard: access time 100-300ms; handles 5500 requests/s per prefix/folder; >=3AZ
- S2 Standard IA: is not the most cost-effective storage medium for infrequent access; >=3AZ
- S3 One Zone-IA: this is the most cost-effective storage medium when IA suffices and an increased chance of data loss is acceptable; =1AZ
- S3 Intelligent Tiering: watches access behavior to uploaded content and re-arranges it to cost effective storage types; >=3AZ
- S3 Glacier Instant Retrieval: long-term archiving with instant retrieval. Storage is generally cheaper than with S3 Standard but retrieval will cost extra; >=3AZ
- S3 Glacier Flexible Retrieval: long-term archiving with recovery time between minutes to 12h. Storage is generally cheaper than with S3 Standard but retrieval will cost extra; >=3AZ
- S3 Glacier Deep Archive: This is the cheapest storage option; Retrieval takes 12 to 48h; >=3AZ

##### Optional Components

Security:

- Buckets are private unless they are explicitly opened to the public (unblock public access). So this is the first step to clear, e.g. for S3 web hosting purposes.
- Setting bucket policies can steer the accessibility of a bucket in its entirety

  ```JSON
  {
      "Version": "2012-10-17",
      "Statement": [
          {
              "Sid": "PublicGetObject",
              "Effect": "Allow",
              "Principal": "*",
              "Action": "s3:GetObject",
              "Resource": "arn:aws:s3:::#BUCKET#/*"
          }
      ]
  }
  ```

- In contrast enabling Object ACLs can provide fine-grained permission down to object level as one can permit public access to every folder/file
- Both options can be combined

Static Website hosting:

Open bucket to public access using unblocking/policies and specify `index.html` and `error.html`.

Versioning:

After versioning is enabled it cannot be removed/cleared, only suspended. Versioning keeps track of all writes, including deletes. MFA is supported by versioning, e.g. to make it harder to delete objects.  
Older versions of an object become inaccessible at first but can be made accessible through ACLs. Once an object is deleted, an empty delete marker is put up as the current version. Deleting the delete marker will restore the latest version of an object before its deletion.

Lifecycle Management:

Manages cost-effectiveness of your operation on the data storage side. Becomes particularly important when using versioning in order to archive/phase out older data becoming irrelevant (or moving rarely used objects to the infrequent access category). Scope can be filtered by prefix/folder/attribute. *Note*: works only in direction of storage tiers of apparent lower cost (e.g., S3 Standard to Glacier), not in opposite direction.

Encryption:

- In transit: SSL/TLS; HTTPS
- Server-side encryption at rest: SSE-S3 &rightarrow; AES 256-bit; SSE-KMS keys (leading to bottle neck in case of many requests per second); SSE-C &rightarrow; Customer provided key
- Client-side encryption at rest: encryption before upload/decryption after download

Server-Side Encryption can be enforced through bucket policy, i.e. a PUT request will have to include a SSE-encryption header in order to be processed by S3.

Data Protection:

- Object lock is used to store data in a WORM model: Governance (few can alter)/Compliance mode (none can alter)
- Define a specific retention period (e.g., 30 days) which prevents changes to an object during that interval
- Legal Hold prevents alterations to the versioning of an object, e.g. for providing regulatory audit trails (user needs s3:PutObjectLegalHold permission)
- Glacier Vault Lock: a way of applying a WORM model to Glacier

S3 Replication:

Renamed from S3 Cross-Region replication as it now can be used between buckets in the same region.
Automatically duplicates bucket content to another bucket.
Versioning needs to be enabled in both buckets and only new objects are replicated after enabling the function. You'll need a CRON/sync task to grab the older data. Delete markers are not replicated by default.

Performance Enhancement:

- Multipart uploads can provide speed ups and recommended for files > 100MB and are mandatory for files larger than 5GB
- For downloads of objects one can used Byte-Range Fetches in a similar manner
- Latency can be reduced by spreading the objects in a bucket over several folders/prefixes.

#### AWS Instance Store Volumes

Ephemeral (short-term) block storage which can host an AMI image tied to the overlying EC2 hardware. AMI is sourced from S3, not a snapshot.
Data persists while the machine is not stopped/terminated or hardware does not fail.  
Not recommended for production, no encryption.

#### Elastic Block Store (EBS)

- General Purpose SSD (gp2/3): for storage of any EC2 OSs - uses cases demanding high number of IOPS (reads/writes up to 16000IOPS) instead of throughput
- Provisioned SSD (io1/2): for use cases demanding even higher IO (DBs/transactional) up to 64000IOPS
- Throughput optimized HDD (st1): for throughput-heavy use cases, e.g. ETLs / Big Data (500MB/s)
- Cold-HDD (sc1): for file servers ((250MB/s)) - cheapest block storage

##### Volume Characteristics

- Are tied to the AZ of the running instance
- Can be resized on the fly
- Can switch volume types on the fly

##### Snapshots

- It's a point in time copy of an EBS Volume saved to S3.
- Are locked to the originating region and need to be copied to a destination region if to be used elsewhere (Snapshot Management Console: snap, copy snap, create image from snap in destination region).
- First snapshot is baseline and takes longer, subsequent snaps are incremental, only.
- Instances should be stopped to capture all data of a machine.

##### Encryption

- AES-based or KMS encryption are possible.
- Latency is hardly affected.
- Copying an unencrypted snap allows encryption on the fly (using KMS). Volume based on snap will be encrypted before copy and remains encrypted.
- Encrypted volumes remain encrypted in snapshot form.
- Root devices can be encrypted.

##### Effects of EC2 Hibernation on EBS

- Hibernation saves a RAM image to the EBS disk additional to whatever data existed on the volume.
- Main benefit is faster boot time over a cold start of a machine.
- On Demand / Reserved Instances, only
- RAM size limit is 150GB
- C,M,R family instances have the feature
- Supported by AWS Linux, Ubuntu, Windows OS
- Hibernation active up to 60 days

#### Elastic File System (EFS)

- EFS is a network file system using NFSv4 protocol. Windows EC2s not supported, currently.
- Encryption using KMS possible.
- It can be mounted on many EC2 instances in multiple AZs simultaneously.
- High throughput (10GB/s), availability (1000s connections) and scalability (PiB) at high cost.
- Use cases: CMS, Web Server Content.
- Two performance levels: general purpose / IO optimized
- Two storage tiers: Standard/ Infrequently Accessed
- Only pay for the storage used, no pre-provisioning required
- Automatic backups / Lifecycle management possible

#### FSx for Windows / Lustre

FSx for Windows: A managed Windows Server running a SMB-based file service. Use cases are distributed Windows-based applications or services: AD, Sharepoint, ACLs, DFS.  

FSx for Lustre: serving data for high performance applications, GB/s throughput, millions of IOPS, sub-millisecond latencies, e.g. for Machine learning, AI, HPC, modelling; can store data on S3

#### AWS Backup

Flexible service to backup not only other services (EC2, EBS, EFS, both Amazon FSx, AWS Storage Gateway)
but also whole AWS accounts, given they were set up using the AWS Organizations service.  
Benefits: Centralization, automation option, compliance/audit friendly.

#### AWS Pricing Calculator

This would be the best way to anticipate what the AWS costs will be.

#### AWS Cost Explorer

AWS Cost Explorer has an easy-to-use interface that lets you visualize, understand, and manage your AWS costs and usage over time. Get started quickly by creating custom reports that analyze cost and usage data. Analyze your data at a high level (for example, total costs and usage across all accounts), or dive deeper into your cost and usage data to identify trends, pinpoint cost drivers, and detect anomalies. This explores previous costs and is not used as a calculator.

#### AWS Shield Advanced

This provides cost protection against a DDoS attack.

#### AWS Serverless Application Repository

The AWS Serverless Application Repository allows users to quickly deploy premade, fully serverless applications to the AWS Cloud.

#### Route 53

Provides several functions:

1. Domain Registration / takes up to three days

2. Hosted Zones: as containers for records, and records contain information about how you want to route traffic for a specific domain. *Public hosted zones* contain records that specify how you want to route traffic on the internet. *Private hosted zones* contain records that specify how you want to route traffic in an Amazon VPC.  
The following record types exist:

    - SOA: Start of authority
    - NS: Nameserver record
    - Alias records, i.e., CNAMES and AWS Aliases, whereas one should always choose the latter when an exam question is provided
    - A records to map domains to IP addresses

3. Routing policies (A/AAAA records) to direct traffic to resources:

    - Simple Routing: just one record evenly splitting traffic between multiple IP addresses, no health checks
    - Multivalue Answer Routing: simple routing with health checks
    - Weighted Routing: splitting traffic based on specific weights, can use health checks as conditional
    - Failover Routing: active/passive, i.e., primary/secondary configuration of, e.g., two ELB targets - simulate by blocking SG ingress of health check
    - Geolocation Routing: route traffic based on its point of origin; filters on continent/country
    - Latency Routing: create a latency resource record set which keeps track of the resource latencies for the required traffic regions. This allows to map traffic to the resource performing best for each request.
    - Geoproximity Routing: Hybrid routing which can be based on location, latency, and availability to route traffic; its GUI-driven (define a traffic flow/policy, a workflow of rules) and uses spatial regions and biases to manipulate the extent of these regions

4. Health checks on resources, including SNS notification

---

### Questions

1. A news media company is using an S3 bucket as a website to serve photos of television personalities within the company. The photos are intended to be served nationwide to local affiliates across the company, but you have found that these photos are being accessed and pirated for other websites not affiliated with the company. What can you do to stop this?

    a) Use a Network Access Control List (NACL) to block the IP address of unauthorized users. How many unauthorized users are there? That is an unknown and could change. Additionally, the offending users could simply change their IP address and it becomes a game of whack-a-mole.

    b) Remove public read access from your bucket, then provide your users with presigned URLs to access the photos. All objects by default are private. Only the object owner has permission to access these objects. However, the object owner can optionally share objects with others by creating a presigned URL, using their own security credentials, to grant time-limited permission to download the objects. When you create a presigned URL for your object, you must provide your security credentials, specify a bucket name, an object key, specify the HTTP method (GET to download the object) and expiration date and time. The presigned URLs are valid only for the specified duration. Anyone who receives the presigned URL can then access the object. [Link](https://docs.aws.amazon.com/AmazonS3/latest/dev/ShareObjectPreSignedURL.html)

2. Your company is currently building out a second AWS region. Following best practices, they've been using CloudFormation to make the migration easier. They've run into a problem with the template though. Whenever the template is created in the new region, it's still referencing the AMI in the old region. What is the best solution to automatically select the correct AMI when the template is deployed in the new region?

    a) Create a condition in the template to automatically select the correct AMI ID. While this would technically work, it's not the best option. Conditions are designed to differentiate between different environments, type of resources, etc. You could create a condition to handle this, but it would require extra work. The best idea would be to use a mapping to automatically fill in the correct ID based on the region you're deploying the template into. [Link](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/mappings-section-structure.html)

    b) Create a mapping in the template. Define the unique AMI value per region. This is exactly what mappings are built for. By using mappings, you easily automate this issue away. Make sure to copy your AMI to the region before you try and run the template, though, as AMIs are region specific.

3. An online media company has created an application which provides analytical data to its clients. The application is hosted on EC2 instances in an Auto Scaling Group. You have been brought on as a consultant and add an Application Load Balancer to front the Auto Scaling Group and distribute the load between the instances. The VPC which houses this architecture is running IPv4 and IPv6. The last thing you need to do to complete the configuration is point the domain name to the Application Load Balancer. Using Route 53, which record type at the zone apex will you use to point the DNS name of the Application Load Balancer?

    a) Alias with a type "CNAME" record set is incorrect because you can't create a CNAME record at the zone apex.

    b) Alias with an A type record set --- Alias with a type "AAAA" record set and Alias with a type "A" record set are correct. To route domain traffic to an ELB load balancer, use Amazon Route 53 to create an alias record that points to your load balancer. An alias record is a Route 53 extension to DNS.

    c) Alias with an AAAA type record set. --- Alias with a type "AAAA" record set and Alias with a type "A" record set are correct. To route domain traffic to an ELB, use Amazon Route 53 to create an alias record that points to your load balancer.

4. A company has an Auto Scaling group of EC2 instances hosting their retail sales application. Any significant downtime for this application can result in large losses of profit. Therefore, the architecture also includes an Application Load Balancer and an RDS database in a Multi-AZ deployment. What will happen to preserve high availability if the primary database fails?

    a) (incorrect) Route 53 points the CNAME to the secondary database instance.

    b) The CNAME is switched from the primary db instance to the secondary. Amazon RDS Multi-AZ deployments provide enhanced availability and durability for RDS database (DB) instances, making them a natural fit for production database workloads. When you provision a Multi-AZ DB Instance, Amazon RDS automatically creates a primary DB Instance and synchronously replicates the data to a standby instance in a different Availability Zone (AZ). Each AZ runs on its own physically distinct, independent infrastructure, and is engineered to be highly reliable. In case of an infrastructure failure, Amazon RDS performs an automatic failover to the standby (or to a read replica in the case of Amazon Aurora), so that you can resume database operations as soon as the failover is complete. Since the endpoint for your DB Instance remains the same after a failover, your application can resume database operation without the need for manual administrative intervention.  
    Failover is automatically handled by Amazon RDS so that you can resume database operations as quickly as possible without administrative intervention. When failing over, Amazon RDS simply flips the canonical name record (CNAME) for your DB instance to point at the standby, which is in turn promoted to become the new primary.

    [Link 1](https://aws.amazon.com/rds/features/multi-az/) and [Link 2](https://aws.amazon.com/rds/faqs/)

    c) (incorrect) A Lambda function kicks off a CloudFormation template to deploy a backup database.

    d) (incorrect) The Elastic IP address for the primary database is moved to the secondary database.

5. Your boss recently asked you to investigate how to move your containerized application into AWS. During this migration, you'll need to be able to easily move containers back and forth between on-premises and AWS. It has also been requested that you use an open-source container orchestration service. Which AWS tool would you pick to meet these requirements?

    a) (incorrect) EC2 and Docker Swarm

    b) (incorrect) ECR

    c) (incorrect) ECS --- This is AWS's proprietary container management tool. It would be best to use EKS, as it meets all the requirements. [Link - EKS](https://aws.amazon.com/eks/)

    d) EKS --- EKS is a managed version of the open-source tool Kubernetes.

6. You have an online store that has recently been featured in a major national news channel, and since then, traffic has been going through the roof. The store consists of a fleet of EC2 instances behind an Auto Scaling group and application load balancer, which then connect to a single RDS instance. The store is struggling with the demand, and you believe this could be a database performance issue. Which options below would help to scale your relational database?

    a) Migrate the database to Aurora Serverless. This would help scale your relational database.

    b) Add read replicas to the RDS database and update your web application to send read traffic to the read replicas. This would help scale your relational database.

    c) (incorrect) Scale your RDS database out so that Multi-AZ is available. This would increase availability but not performance.

    d) Refactor the database to DynamoDB and migrate the database to DynamoDB with DAX enabled. This would help scale your relational database. (!!!!!!)
