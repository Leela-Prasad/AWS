AWS Regions:
AWS has Regions all over the world like US-East-1, US-East-2, US-West-1, Asia-Pacific etc.

And Each Region has Availability Zones(or Datacenters), Mostly each region has minimum of 3 Availability Zones(AZs)/DataCenters so that they are isolated from Disaters.
us-east-1a,us-east-1b,us-east-1c

AWS Consoles are region scoped(means services will be provided or we can select from the nearest AZ) except for IAM and S3(which will have scope as Global).

IAM(Identity Access Management):
IAM is at the centre of AWS, and it is Global scoped.
IAM has
1. Users - physical person
2. Groups - Teams like SOA Team, Devops 
3. Roles - will determine whether a user can use a specific AWS resource or not.

Root account should never be shared or used except for the initial setup.
Policies define what each of above 3 can do or cannot do. Policies are written in JSON
Permissions are governed by Policies.
For Root account we can use Multi Factor Authentication(MFA).
IAM has predefined managed policies.

IAM Federation:
Enterprises usually integrate their own repository of users with IAM so that each user can login AWS Account with their company credentials.
Identity Federation uses SAML Standard(Active Directory).

EC2(Elastic Cloud Compute)
EC2 is one of the most popular AWS Offering
It mainly consists in the capability of 
1. Renting Virtual Machines(EC2)
2. Storing Data on Virtual Drives (EBS)
3. Distributing Load across machines (ELB)
4. Scaling services using an auto-scaling group (ASG)

Security Group:
Security Group acts as a firewall on EC2 Instances.
Security Group controls traffic into or out of EC2 instance.
Here we will define whether clients can ssh or clients can request for http requests etc.
They regulate 
1. Access to Ports
2. Authorised IP ranges - IPV4 and IPV6
3. Control of inbound network (from other to instance).
4. Control of outbound network (from instance to other).

Security Group Good to know
1. can be attached to multiple instances.
2. Locked down to region/VPC Combination.
3. Does lives outside of EC2 (its a firewall not a program that runs in EC2 Instance) - if traffic is blocked EC2 instance won’t see it.
4. Its good to maintain one separate security group for SSH access.
5. If the application is not accessible(timed out), then its a security group issue.
6. If the application gives connection refused error then its an application error or it is not launched.
7. All inbound traffic is blocked by default.
8. All outbound traffic is authorised by default.


Public vs Private vs Elastic IP:

Public IP:
Public IP means the machine can be identified on the internet (WWW).
Must be unique across whole web.
can be geo-located easily.

Private IP:
Private IP means the machine can only be identified on a private network only.
The IP must be unique across private network, but two different private networks can have same IP.
Machines connect through WWW using an internet gateway(Proxy).
Only a specified range of IPs can be used as Private IP.

Elastic IP:
when you stop and start an EC2 instance, it changes it public IP.
If you need to have fixed public IP for your instance you need Elastic IP.
An Elastic IP is a public IPV4 IP you own as long as you don’t delete it.
You can attach to one instance at a time.

With an Elastic IP address you can mask the failure of an instance or software by rapidly remapping the address to another instance in your account.
you can only have 5 Elastic IP in your account.

So try to avoid using Elastic IP
as they often reflect poor architectural decisions.
Instead use a random public IP and register a DNS name to it 
or use a load balancer and don’t use public IP.

By default your EC2 machine comes with 
A private IP for the internal AWS network
A public IP for WWW

When we are doing SSH into EC2 instance
we can’t use private ip as we are not in the same network
we can only use public IP.

If your machine is stopped and started then the public IP can be changed.


#!/bin/bash
yum update -y
yum install -y httpd.x86_64
systemctl start httpd.service
systemctl enable httpd.service
echo “Hello World from $(hostname -f)” > /var/www/html/index.html


IyEvYmluL2Jhc2gKeXVtIHVwZGF0ZSAteQp5dW0gaW5zdGFsbCAteSBodHRwZC54ODZfNjQKc3lzdGVtY3RsIHN0YXJ0IGh0dHBkLnNlcnZpY2UKc3lzdGVtY3RsIGVuYWJsZSBodHRwZC5zZXJ2aWNlCmVjaG8g4oCcSGVsbG8gV29ybGQgZnJvbSAkKGhvc3RuYW1lIC1mKeKAnSA+IC92YXIvd3d3L2h0bWwvaW5kZXguaHRtbA==


EC2 BootStrap:
It is possible to bootstrap our instances with EC2 User Data Script.
bootstrap means launching commands when a machine starts.
This script is only run once at the instance startup.
EC2 User Data Script runs with ROOT user

EC2 Pricing:
EC2 instance pricing(per hour) varies based on these parameters.
Region you are in 
Instance type you are using.
OnDemand vs spot vs Reserved vs Dedicated host.
Linux vs Windows vs Private OS(RHEL, Windows SQL, SLES)

You are billed by the second with a minimum of 60 seconds
i.e.,
if you use for 30 secs you are billed for 60 secs
if you use for 90  secs you are billed for 90 secs

You also pay for other factors such as storage, data transfer, fixed IP Public address, load balancing.

AMI(Amazon Machine Image):
AWS comes with base images such as
1. ubuntu
2. Fedora
3. Red Hat etc

These images can be customised at runtime using EC2 User Data.

But we can create a new AMI with amazon base image and EC2 User Data so that for running every instance we need not specify EC2 User Data Script as it is already installed in  custom AMI.

AMI can be built for Linux or Windows machines. 

Using custom AMI can have following things
1. pre-installed packages needed.
2. Faster boot time(as there is no no need to do at startup using EC2 Userdata)
3. Machine comes configured with monitoring/enterprise software.
4. Security concerns - control over the machines in the network.
5. control of maintenance and updates of AMIs over time.
6. Active Directory Integration out of the box.
7. Installing you app ahead of time (for faster deploys when auto-scaling)

AMI are built for a specific AWS Region.

Load Balancers:
Load Balancers are servers that forward internet traffic to multiple servers(EC2 Instances).

Uses of Load Balancers:
1. spread load across multiple downstream instances.
2. Expose a single point of access(DNS) to your application.
3. Seamlessly handle failures of downstream instances.
4. Regular health checks to your instances.
5. Provide SSL termination(HTTPS) for your websites.
   In ELB(Elastic Load Balancer) there will be SSL connection(HTTPS) between client and ELB, and HTTP between ELB and EC2 Instances.
we can restrict direct access to EC2 Instances by making changes to the security group of EC2 Instances to accept Request from only ELB IP.
6. Enforce stickiness with cookies.
This configuration is done in ELB, and no configuration needed from Application servers.
This stickiness redirects client associated with session to the same EC2 Instance for Request processing.
7. We can configure Load Balancers in Multiple Regions, so High Availability is achieved.
8. Separate public traffic from private traffic.
Here the EC2 Instances will see requests from only ELB private IP, so separating public IP Requests. we can configure ELB to Public or Private based on requirements.


AWS has 3 types of Load Balancers:
1. Classic Load Balancer(v1 - old generation) - 2009
2. Application Load Balancer(v2 - new generation) - 2016
3. Network  Load Balancer(v2 - new generation) - 2017

It is recommended to use v2 new generation Load Balancers.

1. Classic Load Balancer:
In this it will forward the request to instances which have same application running.
If we want to run another application then we may need another Load Balancer which will increase cost and is inefficient design.
The health check is done on a port and a route.
If the response is not 200 (OK) then the instance is unhealthy.

2. Application Load Balancer:
Application Load Balancers(Layer 7) allows to do 
Load balancing to multiple HTTP applications across machines (target groups), each target group corresponds to one application with multiple instances.
Load balancing to multiple applications on the same machine (e.g.: containers)
Load balancing based on the route in the URL.
each route corresponds to one target group.
Load balancing based on hostname in the url.


ALB supports HTTP/HTTPS and web sockets protocols.
The instances doesn’t see the IP of the client directly as we are only getting request from load balancer private IP.
The true IP of the client is inserted in the header (X-Forwarded-For)
we can also get protocol(X-Forwarded-Proto) and port from X-Forwarded-Port

ALB closes the connection(connection termination) at the load balancer end and start a new connection to the instances. 

3. Network Load Balancers:
Network Load Balancers(Layer 4) allows to do 
Forward TCP traffic to the instances
Handle millions of requests per second.
Less Latency ~100ms where as ALB has 400ms
Supports for static IP or elastic IP.

Network Load Balancers are mostly used for extreme performance and should not be the default load balancer to choose.

Good to Know:
Classic Load Balancers are Deprecated.
Application Load Balancers for HTTP/HTTPS and web sockets 
Network Load Balancers for TCP 

CLB and ALB supports SSL Certificates and provide SSL Termination.
All Load Balancers have Health Check Capability.
ALB can route based on hostname or path
ALB are good fit for micro services and Docker.
All Load Balancers has a static hostname. Do not resolve and use internal IP.
NLB can see direct client IP.
4XX are client induced errors
5XX are application induced errors.
If Load Balancer gets 503 means LB has no capacity to accept any instances or target groups.
If LB can’t connect to your application, check your security groups.


*** LB only Load balance instances in the regions where load balancers are running.
suppose LB started in US-east-1a, US-east-1c
and instances started in us-east-1b, us-east-1c
then LB only routes requests to instance present in us-east-1c
so we have to change the LB Availability zone(data center) from us-east-1a to us-east-1b to route requests to both instances


ASG(Auto Scaling Group):
In real life the load on application can change

The goal of ASG is to 
1. scale out EC2 instances to match an increased load.
2. scale in EC2 instances to match a decreased load.
3. Ensure we have minimum and maximum number of machines running.
4. Automatically register new instances to load balancer.

Autoscaling has following attributes.
1. Launch configuration
2. Min/Max size and Initial capacity.
3. Network + subnets(Region + Availability Zones) Information.
4. Load Balancer Information
5. Scaling Policies through custom metrics.

Auto Scaling Alarms:
It is possible to scale an ASG based on CloudWatch alarms.
An Alarm monitors a metric(such as average CPU)
Metrics are computed for overall ASG instances.
Based on alarm
we can scale out(increase number of instances)
we can scale in(decrease number of instances)

Auto Scaling Custom Metric
we can autoscale based on custom metric(e.g.: number of users connected)

for this we have send custom metric from application to cloud watch(PutMetricAPI)
create cloud watch alarm to react to low/high values.
use cloud watch alarm as scaling policy for ASG.

Scaling policies can be on CPU,Network or even on custom metrics or based on schedule(if you know your visitor patterns)
ASG uses launch configuration for new EC2 instances, so you can update ASG to have new launch configuration.
IAM roles attached to ASG will be assigned to EC2 instances.
ASG are free. you need to pay for underlying resources being launched.
Having instance under ASG means that if they get terminated for whatever reason ASG will restart them.
ASG can terminate instances marked as unhealthy by LB.

 
EBS(Elastic Block Store):
An EC2 Instance lost its root volume(main drive) when it is terminated, so we need a way to store instance data somewhere.
Storing of Instance data is done using EBS.
EBS is a network drive you can attach to your instances while they run.
since it is a network drive a little bit of latency is involved for communication between EC2 instance and EBS network drive.
It can be detached from one EC2 instance and attach it to another EC2 instance, but EBS is locked to Availability Zone.
An EBS in us-east-1a cannot be used in us-east-1b.
To move the volume to another AZ you first need to snapshot it and create a EBS out of snapshot.
EBS will be billed for the entire provisioned capacity(size in GBs and IOPS)
IOPS - IO Operations per second.
if you have taken 100GB EBS and use 10GB AWS will charge for entire 100GB.
but for snapshot it will be charged based on the usage.

EBS Volume Types:
GP2(SSD) - General Purpose SSD volume that balances price and performance for a wide variety of workloads
IO1(SSD) - Highest performance SSD for mission critical low latency or high throughput workloads. It is very expensive.
ST1(HDD) - Low cost HDD volume designed for frequently accessed throughput intensive workloads.
SC1(HDD) - Low cost HDD volume designed for less frequently accessed workloads.
ST1 and SC1 are mainly used for BigData Applications.

You can only increase the EBS volumes:
1. Size (any volume type)
2. IOPS (only in IO1)

** After resizing EBS volume you need to repartition your drive.

EBS Snapshots:
EBS Volumes can be backed up using snapshots.
Snapshots are used for 
1. Backup - ensure you save your data incase of catastrophe.
2. Volume Migration 
   Resizing a volume down
   Changing volume type.
   Encrypt a volume.

EBS Encryption:
when you create a encrypted EBS volume you get the following:
1. Data at rest is encrypted inside the volume.
2. All the data in flight moving between instance and volume is encrypted.
3. All snapshots are encrypted.

Encryption and decryption are handled transparently by AWS using KMS Keys(AES-256).
Encryption has minimum impact on latency so we should use it. 

EBS Brain Dump:
EBS can be attached to only one instance at a time.
EBS are locked at AZ Level.
Migrating an EBS Volume means first backing it up with snapshot and then recreating it in other AZ.
EBS backup use IO, so you shouldn’t run them when your application is handling lot of traffic.
Root EBS is lost when EC2 is terminated.


Route 53:
Route 53 is a Managed DNS(Domain Name System).
DNS is a collection of rules and records which help clients(browsers) understand how to reach a server through URLs.
In AWS most common records are 
1. A - URL to IPv4
2. AAAA - URL to IPv6
3. CNAME - URL to URL
4. Alias - URL to AWS Resource.
Prefer Alias over CNAME for referring to AWS resource(due to performance reasons).

RDS(Relational Database Service):
It is a managed DB Service by AWS.
Below Databases are managed by AWS.
1. Postgres
2. Oracle
3. MySQL
4. Maria DB
5. Microsoft SQL Server
6. Aurora (AWS Proprietary database)

Advantages of using RDS:
1. Managed Service.
2. OS Patching Level.
3. Continuous backups and restore to specific timestamp.
4. Monitoring dashboards.
5. Read replicas for improved read performance.
6. Multi AZ setup for Disaster Recovery.
7. Maintenance windows for upgrades.
8. Scaling Capability (vertical and horizontal)
9. You can’t SSH into your instances since it is managed by AWS.

RDS for Read Replicas for read scalability:
we can have upto 5 Read Replicas.
Replicas can be Within AZ,cross AZ or cross region.
Replication is Asynchronous so reads will be eventually consistent.
Replicas can be promoted to their own DB.
Applications must update connection string to leverage read replica.
each replica will have different connection string.

RDS Multi AZ(Disaster Recovery):
Here it is Sync Replication.
One DNS name - so app failover to standby will be automatic
With this we will have High Availability
No manual intervention in apps during failover from master to standby.
This is not used for Scaling.

RDS Backups:
Backups are automatically enabled in RDS.

Automated Backups:
Daily full snapshot of the database.
capture transaction logs in real time.
ability to restore to any point in time.
7 days retention(can be increased to 35 days).

RDS Encryption:
Encryption at rest capability with AWS KMS- AES-256 encryption
we can use SSL certificates to encrypt data to RDS in flight.
To enforce SSL:

In Postgres we need to set one parameter
rds.force_ssl=1 in AWS RDS Console.

In MySQL within DB we need to execute below query
GRANT USAGE ON *.* TO ‘mysqluser’@‘%’ REQUIRE SSL;

RDS Security:
RDS databases are usually deployed within a private subnet not in a public one so that only EC2 instances will access RDS.
RDS Security works by leveraging security groups - so that inbound properties control who can communicate with RDS.
IAM Policies can help to manage RDS.
Traditional username and password can be used to login to database.


RDS vs Aurora:
Aurora is a proprietary technology from AWS
Postgres and MySQL are both supported as Aurora DB means your drivers will work as if Aurora was postgres or MySQL database.
Aurora is AWS cloud optimised and claims 5X performance improvement over MySQL on RDS over 3X performance of Postgres on RDS.
Aurora storage automatically grows in increments of 10GB upto 64TB.
Aurora can have 15 replicas whereas MySQL has 5
Failover in Aurora is instantaneous. High Available native.
Aurora cost more than RDS(20% more) - but it is more efficient. 

Elastic Cache:
It is a managed Cache from AWS.
AWS has following caches.
1. Redis
2. Memcached

Cache are in-memory databases with really high performance, low latency.
It helps to reduce load off from databases for read intensive workloads.
It helps to make application stateless by storing user session details in cache, if you are using Network Load balancer, as it doesn’t have Session stickiness.
It provides Write Scalability using Sharding.
It provides Read Scalability using Read Replicas.
It can be run in multi AZ with failover capability for disaster Recovery.
AWS takes care of OS maintenance/ patching, optimazations, setup,configuration, monitoring, failure recovery and backups.

Elastic Cache Usecases:

1. DB Cache:
a. Application queries Elastic Cache if not available then it get from RDS and store in ElasticCache.
b. It helps to relieve load in RDS.
c. Cache must have an invalidation strategy to make sure only the most current data is present in cache.

2. User Session Store:
a. Users logs into any of the application.
b. application writes data into ElasticCache.
c. User hits another instance of our application.
d. The instance retrieves the data and the user is already logged in.

Redis Overview:
Redis is in memory key-value store.
It is low latency <1ms
Redis Cache survives reboots by default(by persistence)
Supports Read Replicas
Supports Multi AZ for automatic failover for disaster recovery.

Memcached Overview:
Memcached is an in memory object store.
cache doesn’t survive reboots.

VPC(Virtual Private Cloud):
With in a region you can create a VPC.
Each VPC Contains subnets(means networks).
Each Subnet must be mapped to AZ.
It is common to have public Subnet.
It is common to have private Subnet.
It is common to have multiple subnets per AZ.

Public Subnet contains
1. Load Balancers
2. Static Websites.
3. Files.
4. Public Authentication Layers.

Private Subnet contains.
1. web application servers.
2. Databases.

Public and Private subnets can communicate if they are in same VPC.

VPC Points:
All new accounts come with default VPC.
It is possible to use VPN to connect to VPC. (means accessing all private IP straight from your laptop).
VPC Flow logs allow you to monitor traffic within in and out of your VPC(useful for security,audit and performance).
VPC are per account per region.
subnets are per vpc per az
you can peer vpc(within or across accounts) to make it look like they are part of same network. 

S3(Simple Storage Service):
Amazon S3 allows people to store objects(files) in buckets(directories)
Buckets must have “globally unique name”, not unique to your account but unique across all AWS accounts in the world.
Buckets are defined at Region level, so Global scope.
Bucket Naming Convention:
1. No Uppercase
2. No Undersccore
3. 3-63 long
4. Not an IP
5. Must start with lowercase letter or number.

Objects(files) have a key, and the key is FULL PATH.
eg:
    <mybucket>/my_file.txt
    <mybucket>/myfolder/another_file.txt

There is no concept of directories in buckets, but UI will trick you in that way. to identify a file you need to give its key i.e., full path.
Keys are very long names that contains slashes(“/“)

Object Values are the content that is going to be stored
Its max size is 5TB.
If you are uploading object more than 5GB then we must use “multi-part upload” else upload will fail.
Objects have versionid only if versioning is enabled.

S3 - Versioning:
You can version your files in S3.
Versioning is enabled at the bucket level not at file level.
Same Key overwrite will increment the version 1,2,3…
It is recommended to version your buckets because to protect against unintended deletes or ability to restore to a specific version.
Any file that exist in the bucket before enabling versioning will have version as “null”

** Versioning will take additional space in S3 Bucket.  

S3 Encryption:
There are 4 methods to encrypt objects in S3
1. SSE-S3 : encrypt s3 objects using keys handled and managed by AWS.
2. SSE-KMS : leverages AWS Key Management Service(KMS) to manage/choose encryption keys.
3. SSE-C : this will be used when you want to manage your own encryption keys.
4. Client Side Encryption

1. SSE-S3:
encryption using keys handled and managed by AWS.
Object is encrypted server side.
AES-256 is the encryption type.
We must have to set header
“x-amz-server-side-encryption” : “AES256”

2. SSE-KMS:
encryption using keys handled and managed by KMS.
KMS Advantages : user control + audit trail
user can choose keys and key rotation.
Object is encrypted server side.
We must have to set header
“x-amz-server-side-encryption” : “aws:kms”

3. SSE-C:
encryption using data keys fully managed by customer outside of AWS.
* Amazon S3 doesn’t store encryption key you provide.
HTTPS must be used because you are providing encryption key for every request.
Encryption key must be provided in HTTP Header, for every HTTP request.

4. Client Side Encryption:
we can use client library such as Amazon S3 Encryption client.
client must encrypt data themselves before sending to S3
client must decrypt data themselves when retrieving from S3
Customer fully manages keys and encryption cycle

Encryption in Transit(SSL):
HTTP endpoint : non encrypted
HTTPS endpoint : encryption in flight.

It is recommended to use HTTPS endpoint
HTTPS is mandatory for SSE-C because you are sending key in HTTP request.
Encryption in flight is also called SSL/TLS.

S3 Security:
S3 Objects operations can be controlled in 2 ways.
1. User based
   This is done via IAM Policies - which defines which API calls(like get object, put object, delete object etc) should be allowed for a specific user from IAM console.

2. Resource Based
   This can be done in 3 ways.
   Bucket Policies - bucket wide rules from S3 Console.
   Object Access Control List - less commonly used.
   Bucket Access Control List - less commonly used.

S3 Bucket Policies:
It is JSON based policies
  Resources : buckets and objects
  Actions : set of API(get/put/delete object) to allow or deny
  Effect : Allow/Deny
  Principal : The account or user to apply policy 

Use S3 Bucket Policies to 
grant public access to bucket.
force object to be encrypted at upload.
grant access to another account(cross account).

Networking:
Supports VPC endpoints(means instances in vpc can access S3 without www internet)

Logging and Audit:
S3 access logs can be stored in another bucket.
API calls can be logged in AWS Cloud Trail.

User Security:
MFA(Multi Factor Authentication) can be required in versioned buckets to delete objects.
Signed Urls: URLs that are valid only for a limited time (e.g.: premium video service for logged in users)

Bucket Policy for user to put object in SSE-S3 encryption mode.
{
    "Version": "2012-10-17",
    "Id": "Policy1545141956813",
    "Statement": [
        {
            "Sid": "Stmt1545141873558",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::thebucketofleela/*",
            "Condition": {
                "Null": {
                    "s3:x-amz-server-side-encryption": "true"
                }
            }
        },
        {
            "Sid": "Stmt1545141948318",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::thebucketofleela/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-server-side-encryption": "AES256"
                }
            }
        }
    ]
}


Policy for Public Access:
{
    "Version": "2012-10-17",
    "Id": "Policy1545145895564",
    "Statement": [
        {
            "Sid": "Stmt1545145892099",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::thebucketofleela/*"
        }
    ]
}

CORS(Cross Origin Resource Sharing)
If you request data from another S3 bucket, then you need enable CORS.
CORS allows you to limit number of websites that can request your files in S3.

EBS(Elastic Block Store) vs S3(Simple Storage Service) vs EFS(Elastic File System):

1. EBS vs S3:
If we want to store an excel file of 100MB

then in EBS this file will be stored in multiple blocks(where block size is 512kb), suppose if 2 columns are changed then only blocks which has these cells will be changed. So this has faster performance and is suitable for fast read write operations.

S3 is a object store. everything is stored as objects.
In S3 this file will be stored as an object and change in 1 column also needs deletion of entire object and creation of entire object. so this is suitable for multi read operations.
S3 has WORM(Write Once Read Many) principle.
when we have static data like videos or images S3 is highly recommended.
In our projects Nzips can be stored in S3.

S3 is not specific to region, it is Global and can be accessed by all EC2 Instances whereas EBS is specific to AZ.

S3 is cheaper when compared to EBS.

S3 has unlimited storage option where as EBS can have maximum of 16TB.

Changes in S3 will not be reflected immediately, but takes time i.e., it will have eventual consistency.
S3 is not suitable for hosting OS or Databases where files will change frequently.

2. EBS vs EFS:
EFS is newly introduced.
EBS can be attached to only one EC2 instance whereas EFS can be attached to multiple EC2 instances.
EBS is per AZ whereas EFS is per region.
EFS doesn’t need size to be defined like EBS, so No resizing need to be done.
EFS is costlier than EBS.


AWS CLI(Command Line Interface):
So far, we interact with AWS Services(like EC2,S3,Dynamo DB etc) manually through UI Console, but using command line interface we can interact with AWS Services programatically, this will be helpful in automation.

These programatic interactions can be done in the following ways:
1. Using AWS CLI on local computer.
2. Using AWS CLI on EC2 instance.
3. Using AWS SDK on local computer.
4. Using AWS SDK on EC2 instance.
5. Using AWS Instance Metadata Service for EC2

AWS exposes a standard private url which will provided metadata about EC2 instance like
hostname
ami id
security groups
IAM Role name (we cannot get IAM Policy information from this)


Private URL: http://169.254.169.254/latest/meta-data/

when you access http://169.254.169.254/latest/meta-data/iam/security-credentials/<Role-name> then you will get a json which contains 
AccessKeyId, SecretAccessKey,Token,Expiration
This credentials are created on the fly and will be valid only for 1 hour.

{
  "Code" : "Success",
  "LastUpdated" : "2018-12-20T03:11:53Z",
  "Type" : "AWS-HMAC",
  "AccessKeyId" : "ASIARW5CUYOB75KLVPY3",
  "SecretAccessKey" : "WmSVsax06XvUreacOYtGAB0XYhRDW3DdKRjcG6UG",
  "Token" : "FQoGZXIvYXdzEE0aDIiVSTTDt5/XZm1x/yK5A4T5twA/aFMVDKUjTecr202AFh4KeZlHr3L3KlyjVXk9ZUnuT3xFxQjmoM+50xYU1M6gP+1OGb2Gi7v6V2FIii0sEfpJwn/rm8pjSHZhGKPr4IA09eICv1z5aSu32buZ9H43DHJs33s999e4uBKlIFkxutBIaxrMpl0qPwznuyXwvapnpwQs2pHNcCnkgsctmTStk5QJgftUniGSEfkkt13GHsGQIPKSAkIQI3wIHAlfBPhQnmZpNI2NYMkM4dKF4foaCvJm98+xPdsIred80ifaWG9Stq7SCNrE4tiy+CCwJifbCS77z5PcTjzMirIPBpj7F3kquGGEB95W3HYqNZo1pYJwDyMaxGY70YtpDTESeFeCwPmoceIImzD1tRDXNe1+SHVAoCnmboHKtwglIWQTJp5ZOk5Y9FlyFWN9fUwt+iiuZfRlUVsYbnIfLO6itynZm3NDP4B8PYdT2lM9yGprT9UOJCOlmQ4sGR7ypBvjLBSU7YLHytdsIKtTRjh2/zvDyd01n/TX3boEXFdmlEK2xg/3UGKKw9mTPTMiQ41V+ENVkX9VgaxBECdWf42Pg8xZkwwuNMD8cijnkOzgBQ==",
  "Expiration" : "2018-12-20T09:46:35Z"
}

Note these credentials are valid for only 1 hour.


AWS CLI Installation on mac:
Prerequisites : Python 2 version 2.6.5+ or Python 3 version 3.3+

1. Download the AWS CLI Bundled Installer.
   curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"

2. unzip zip file
   unzip awscli-bundle.zip

3. Run the install program.
   sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws

AWS CLI Configuration on Local Computer:
1. Get the AccessKeyId,SecretAccessKey from IAM Console for the user.
2. Run “aws configure” on terminal
it will ask for AccessKeyId,SecretAccessKey, just provide the details.
Region you have to provide without alphabet character at the end 
ap-south-1
this will consider both ap-south-1a,ap-south-1b
If you don’t specify a default region then us-east-1 will be chosen by default.
format you can leave it default.

Now your computer is configured to connect to AWS Resources through CLI.

This aws configure will create hidden folder (.aws) on home directory.
This folder will contains 2 files.
1. config - this will have region details
2. credentials - this will have AccessKeyId,SecretAccessKey

S3 operations using AWS CLI:
aws s3 ls
aws s3 ls s3://somebucket
aws s3 cp s3://somebucket/someimage.png image.png
aws s3 mb s3://secondbucket
aws s3 rb s3://secondbucket


AWS CLI on EC2:
we can also run “aws configure” on EC2 instance and provide AccessKeyId,SecretAccessKey to access other AWS Resources such as S3 but this is NOT ACCEPTABLE WAY.
Reason: If EC2 is compromised then some other user can misuse your credentials.
        If EC2 is shared other people may perform AWS actions while impersonating you.
So this way is SUPER INSESCURE.

Best way to deal with this problem is to use AWS IAM Roles.
We can attach IAM Roles to EC2 instances.
IAM Roles can be attached with multiple policy telling what exactly EC2 instance should be able to do.

I created a IAM Role “CustomRole”
for this Role I attached 2 policies
1. S3ListPolicy 
2. EC2-Run-Policy 

1. S3ListPolicy(Read Policy): This can list all buckets in S3 and list a specific bucket(arn:aws:s3:::thebucketofleela) means it can list all objects inside arn:aws:s3:::thebucketofleela bucket.
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": "s3:ListBucket",
            "Resource": "arn:aws:s3:::thebucketofleela"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "s3:ListAllMyBuckets",
            "Resource": "*"
        }
    ]
}

2. EC2-Run Policy: This policy given write access to run ec2 instances in AWS Account.

{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "ec2:RunInstances"
            ],
            "Resource": "*"
        }
    ]
}

Policy Simulator:
We can go to policy simulator and can test policies that are assigned to that role using GUI.
Note: AWS Policy simulator will actually run actual requests against AWS Resources.
sometimes we don’t need to run the request but we need to know whether policy is working as excepted or not then for this use case we will AWS CLI Dry Runs.

AWS CLI Dry Runs.
Dry Run for testing a policy which can create a new EC2 instance.
aws ec2 run-instances —dry-run —image-id <AMI-ID> —instance-type t2.micro

This command will not start a new ec2 instance but it will say whether command will be succeded or not. This is happening because —dry-run flag.

AWS CLI STS Decode Errors:
when you run API calls using —dry-run flag  you can get a long error message.
This error message can be decoded using STS command
sts decode-authorization-message —encoded-message <error-mesage>

AWS SDK(Software Development Kit):
if you want to perform actions to AWS directly from your application code then we will use AWS SDK.
we can use AWS SDK when coding against AWS Service such as Dynamo DB.
AWS CLI uses python SDK(boto3)

AWS SDK Credentials Security:
It is recommended to use default credential provider chain.
Default credential provider chain work seamlessly with
AWS credentials at ~/.aws/credentials(this is recommended only on computers or on premise)
Instance Profile Credentials using IAM Roles(for EC2 machines)
Environment variables(AWS_ACCESS_KEY, AWS_SECRET_ACCESS_KEY)

Exponential Backoff:
Any API that fails because of too many calls needs to be retried with Exponential Backoff
These apply to rate limited API.
Retry mechanism included in SDK API calls.

1st request failed - wait 1 sec
2nd request failed - wait 2 sec
3rd request failed - wait 4 sec
4th request failed - wait 8 sec

CLI Profiles:
Suppose if you have multiple AWS accounts then you can configure CLI for multiple accounts in a single computer using profiles.

To configure 2nd profile then we need to run below command
aws configure —profile <profile-name>
aws configure —profile secondaccount

Running commands
aws s3 ls  -> runs against default(first) profile
aws s3 ls —profile secondaccount   -> runs against secondaccount profile

Elastic Beanstalk:
When we want to deploy an application we have to manually create Load Balancer,Auto Scaling Groups,RDS,Elastic Cache etc components for an application to be deployed.

Elastic Beanstalk will create all these components automatically.
So deploying applications using Elastic Beanstalk is preferred.

Elastic Beanstalk is one view to configure all components such as ELB,ASG,RDS,Cache etc.
and deploying an application on AWS.
Elastic Beanstalk is free but you have pay for underlying instances.

Deployment strategy can also be configured but performed by Elastic BeanStalk.
Three Deployment Architecture models:
1. Single Instance Deployment - good for dev.
2. LB + ASG - good for production and pre-production web applications.
3. ASG - good for non-webapps in production(workers etc) i.e., background jobs,batch jobs etc.

Just the application code is the responsibility of developer.

Elastic Beanstalk has three components:
1. Application
2. Application Version - each deployment gets assigned a version
3. Environment Name - dev,test,prod etc - We can use custom names as well.

You can deploy application versions to environments and can promote application versions to next environment.
Rollback feature to previous application version is also available.
Elastic Beanstalk supports many languages like Java,.Net,Go,PHP,Ruby etc.

ElasticBeanstalk Deployment Modes:
1. Single Instance
   Great for dev
   EB DNS Name -> Elastic IP of EC2 instance.

2. High Availability with Load Balancer
   Great for prod
   EB DNS Name -> ELB DNS Name

Elastic Beanstalk Deployment Options for Updates:
1. All at once:
   Fastest deployment, but instances aren’t available to serve traffic for sometime.
   Application has downtime.
   Great for dev.
   No additional cost.

2. Rolling:
   Update few instances at a time(also known as bucket) and then move on to the next bucket once the first bucket is healthy.
   Application is running at below capacity during deployment.
   No additional cost.
   At some point of time application is running both versions simultaneously.
   Long Deployment.
   No downtime

3. Rolling with additional batches:
   It is similar to Rolling, but for the first batch it spins up new instances so that application is available at full capacity during deployment.
   Small additional cost because new instances that spins up for first batch.
   At some point of time application is running both versions simultaneously.
   Longer Deployment.
   Good for prod.
   No downtime.
4. Immutable:
   spins up new instances in new ASG and deploy new version to the instances in new ASG and then swaps all instances when everything is healthy.
   New code is deployed to new instances on a temporary ASG.
   High cost, double capacity during deployment because new version is deployed on new instances on temporary ASG.
   Longest Deployment.
   Good for prod.
   No downtime.
   Quick Rollback incase of failures(just terminate temporary ASG).

5. Blue/Green
   This Deployment option is not a direct feature of Elastic Beanstalk.
   Zero downtime and release facility(means we can test actual production traffic on the stage/Green Environment)
   In this we create new stage environment and deploy new application version there
   In this only we will create new environment above methods use the same environment.
   We use Route53 to setup weighted policies to redirect little bit of traffic to the stage environment.
   The new environment(green) can be validated independently and rollback in case of issues.
   If everything works fine then we will use Beanstalk to swap urls to the stage environment and 100% production traffic will go through stage environment.


Elastic Beanstalk Extensions:
We have to package the code in a zip file, so that it can be deployed to Elastic Beanstalk.

All the configuration parameters set in the UI(like LB,ASG,RDS etc) can be configured with code using files.
We can modify default settings using - ‘option_settings’ field

These files ends with extension .config (e.g.: logging.config)
config files should be in YAML/JSON format.
These config files should be present in .ebextensions/ directory and this directory is at the root of the source code.

** Resources managed by .ebextensions get deleted if the environment goes away.

Elastic Beanstalk CLI:
we can install additional CLI called “EB cli” in aws cli so that we can interact with elastic beanstalk programatically.
Basic Commands are
eb create
eb status
eb health
eb deploy
eb events
eb logs
eb open
eb config
eb terminate

These commands are helpful in automating deployment pipelines.

Under the hood Elastic Beanstalk relies on Cloud Formation.

Elastic Beanstalk Deployment Mechanism:
1. Describe dependencies
(requirements.json for python, package.json for node.js)
2. Package code as zip file.
3. zip file is uploaded to EC2 instances so each EC2 machine will resolve dependencies(which is slow)
4. To improve the deployment performance and speed Package all dependencies with source code(fat jar).


{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Sid": "eb-ad78f54a-f239-4c90-adda-49e5f56cb51e",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::117915829123:role/aws-elasticbeanstalk-ec2-role"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::elasticbeanstalk-ap-south-1-117915829123/resources/environments/logs/*"
        },
        {
            "Sid": "eb-af163bf3-d27b-4712-b795-d1e33e331ca4",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::117915829123:role/aws-elasticbeanstalk-ec2-role"
            },
            "Action": [
                "s3:ListBucket",
                "s3:ListBucketVersions",
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::elasticbeanstalk-ap-south-1-117915829123",
                "arn:aws:s3:::elasticbeanstalk-ap-south-1-117915829123/resources/environments/*"
            ]
        },
        {
            "Sid": "eb-58950a8c-feb6-11e2-89e0-0800277d041b",
            "Effect": "Deny",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:DeleteBucket",
            "Resource": "arn:aws:s3:::elasticbeanstalk-ap-south-1-117915829123"
        }
    ]
}
