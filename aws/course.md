# AWS

## Day 1

###  what is cloud ?
- cloud provides on demand compute resources and services over the internet.
- it works with base concept of virtualization.
- cloud formed with large capacity of compute resources and virtual resources gets created on top of it based on on-demand basis.

create account in AWS console.

## Day 2
### IAM 
Identity and Access Management is a web service that manages access to AWS services. It helps in providing authentication and authorization for AWS resources.

It helps ensure that the authorize people or machines can access right assests at the right time.

Generally, the root user creates DevOps user account with admin privileges. DevOps user creates the user account and assigns necessary permissions.

Authentication is a process of verifying user's identity and Authorization is a process of determining what user can access.

![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_IAM.JPG)

Users - User creation comes under authentication part.
Policies - This takes care of what the user can do. This define the permissions of an entity or resource. After user creations, policies gets attached to user. 
Groups - Groups to categorize users according to their job roles. The policies are attached to groups so that everytime the new user gets created, it can be easiliy added to the group instead of adding policies one by one.

Roles - Roles are entities that grant access to AWS services and resources by providing temporary credentials. For example, application (from outside of AWS) that runs on private cloud or on premise needs to access DB service that runs on AWS to access customer data, then the application can use roles to access DB service. Here, roles will have temporary credentials. Roles are similar to user but not as 100% like users.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_roles_usecase.JPG)

Creating simple user dev-user-101, no attaching to groups/policies.

![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_simple_user_creation.JPG)

you will be able to download the csv file with contents as,
```
User name	Password	Console sign-in URL
dev-user-101	test@1234	https://381491823509.signin.aws.amazon.com/console
```
in above, 381491823509 is the account id. Login with account id, user name and password.

attempted to create bucket from dev-user-101 account and no permission,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_s3_bucket_creation_failed.JPG)

adding permission from devops user or root user for dev-user-101 account.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_add_permission.JPG)

In AWS, there is AWS managed permission and customized permission or policies. Now after provide S3FullAccess we are able to see s3 buckets.

Create groups, attach policy/permission to the group. Use only IAM user even for DevOps or admin user, dont use root user. IN below example, create testers-group with s3fullaccess policy,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_test_group_with_s3access.JPG)

Now adding test-user-501 to testers-group,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_test_group_with_testuser.JPG)

## Day 3

### EC2 - Elastic Cloud Compute
EC2 provides businesses with the ability to run applications and workloads in the AWS public cloud.
AWS has regions and each region has Availability zones.
Availability zone: Enable you to operate production application that are more highly available, fault tolerant, and scalable than possible when using a single data centre.
Regions : Regions are collection of zones. Zones have high band width, low latency network connections to other zones in the same region. 
If the customer from US wants to access application, then have EC2 instance running in regions in US to serve response with less delay and that is a low latency. If EC2 instance created in Mumbai region, then there are chances that delay will be there which is a high latency.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_regions.JPG)

The default storage is 8GB which is enough for application to run.

You will be able to connect to EC2 instance via Putty or Moba xterm with the Public IP address of EC2 instance,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_connect_to_EC2_instance.JPG)
