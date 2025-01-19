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

Create groups, attach policy/permission to the group. Use only IAM user even for DevOps or admin user, dont use root user. In below example, create testers-group with s3fullaccess policy,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_test_group_with_s3_access.JPG)

Now adding test-user-501 to testers-group,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day2_test_group_with_testuser.JPG)

## Day 3

### EC2 - Elastic Cloud Compute
* EC2 provides businesses with the ability to run applications and workloads in the AWS public cloud.
AWS has regions and each region has Availability zones.
* Availability zone: Enable you to operate production application that are more highly available, fault tolerant, and scalable than possible when using a single data centre.
* Regions : Regions are collection of zones. Zones have high band width, low latency network connections to other zones in the same region. 
* If the customer from US wants to access application, then have EC2 instance running in regions in US to serve response with less delay and that is a low latency. If EC2 instance created in Mumbai region, then there are chances that delay will be there which is a high latency.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_regions.JPG)

The default storage is 8GB which is enough for application to run.

You will be able to connect to EC2 instance via Putty or Moba xterm with the Public IP address of EC2 instance,

![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_connect_to_EC2_instance.JPG)

I was unable to connect, so as part of troubleshooting found that EC2 didnt have public subnet so recreated VPC with private & public subnets. And then assigned this public subnet to my EC2,

![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_EC2_with_public_subnet.JPG)

Also i disconnected my office VPN and then tried ssh from git bash as, 
![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_ssh_EC2_instance.JPG)

```
$ chmod 600 aws-login-key.pem
$ ssh -i aws-login-key.pem ubuntu@34.239.187.60
```

From EC2 instance,
```
$ sudo su -
# sudo apt update
# sudo apt install fontconfig openjdk-17-jre
# sudo apt install jenkins
# systemctl status jenkins
```

Install Jenkins applications, edit security group to allow incoming port traffic to 8080.

Now enable port 8080 with security group,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_inbound_port_8080.JPG)

Open jenkins web page with public ip http://34.239.187.60:8080
![image](https://github.com/balathecoder/newproject/blob/master/aws/day3_jenkins_webpage.JPG)

## Day 4

### VPC
Virtual Private Cloud is a virtual network that allows you to launch AWS resources in a logically isolated section of the AWS cloud.

VPC for a company and subnet for projects within a company.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day4_VPC_and_Subnets.JPG)

* ![image](https://github.com/balathecoder/newproject/blob/master/aws/day4_VPC_concepts2.JPG)
* Inside a VPC, there are public subnets and private subnets. Entire IP address is allocated to VPC. 172.16.0.0/16 for 65536 IP addresses where 16 represents allocates first 16bits for network ip(172.16) and remaining 16bit for host ip.
Each subnet can have EC2 instances depends on the project requirements. IP address range gets sub divided for each subnet. 172.16.1.0/24 for 256 IP addresses wherein first 24 bits for network ip and remaining 8bit for host ip. Subnets are smaller range of IP addresses. Within VPC any resource from a subnet can communicate with other resources from other subnets within VPC.
* Public subnet is the one that a user access inside a VPC. Load balancers will be on Public subnets.
* Public subnet connects to the Internet using Internet Gateway.
* Private subnets where all application resources resides. This doesnt have access to outside world.
* To connect to resources outside of VPC, use S3 Gateway to connect to S3 buckets.
* Through the public subnet, the user enters into the VPC.
* Load Balancer that forwards requests to application.
* Route table using which User from Internet reaches application from Load Balancer. This defines how the request goes to the application.
* Once reaches Ec2 instance, still something can block request which is Security group. Once it allows request to port number, then the request can reach to application.
* NACL within subnet if you want to repeat security group concept to multiple EC2 instances, then NACLs can be used. This is basically automation for security group.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day4_VPC_concepts.JPG)

NAT - Let say application wants to download something from internet. If my application is xyz server, and needs to download a package from internet, then the external world should not know IP address of my xyz server, in that case IP address has to be masked. Masking of IP address is called as NAT gateway. This basically helps to download resource from Internet along with that it masks IP address of application.

![image](https://github.com/balathecoder/newproject/blob/master/aws/day4_why_VPC_with_example.JPG)

## Day 5

### Security Group and NACL
Security Group and NACL are both tools for controlling access to AWS resources within a VPC. The main difference between two is that Security group control access at instance level, while NACL control access at the subnet level.

NACL helps to define access level at organization level. So if deny access list is defined in subnet level with NACL, the same port/ip cant be allowed in Security group level. Similarly, if you want to allow port 50 for all EC2 instance in a subnet, then you can do that in NACL.

In AWS, security is a shared responsibility. 

![image](https://github.com/balathecoder/newproject/blob/master/aws/day5_SG_and_NACL.JPG)

Let say in a subnet, if there are 10000 EC2 instances, then you can apply allow/deny rules at NACL only once which helps you in saving time of applying time for all 10000 EC2 instances. Remember that, SG has only allow rules.

![image](https://github.com/balathecoder/newproject/blob/master/aws/day5_SG_and_NACL2.JPG)

When you do VPC creation, the below components gets created. It should have public subnet for the resources within VPC to connect to Internet.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day5_VPC_creation.JPG)

Exercise :
* Create VPC with default private and public subnets - my-vpc-01
* Create EC2 instance which uses my-vpc-01 VPC and uses a public subnet with Public IP enabled.
* Now run a application on the EC2 instance,
```
$ python3 -m http.server 8000
```
* Try to access this web application from browser with http://<pubic_ip>:8000
* You wont be able access, try to provide access in both ways,
- via Security Group,
- via Network ACLs for the public subnet and allow port 8000 for ec2 instance within publich subnet.

## Day 6

### Route53
Route53 provides DNS as a service as similar like EC2 for Compute service, EKS for Kubernetes as a service. Using Route53 we can do domain registration, we can update DNS records using hosted zones. It also performs health checks.

The URL request comes via Internet Gateway and once it reaches route53, it routes it to the appropriate LoadBalancer.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day6_route53.JPG)

Once your are done with your application, then very first thing that you need to do is domain registration. Domain registration can happens with AWS as AWS also sells domain name or outside also we can buy the domain name i.e:GoDaddy or etc

![image](https://github.com/balathecoder/newproject/blob/master/aws/day6_route53_setup.JPG)

Documentation :https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html
## Day 7
Project,
![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_project.JPG)

![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_project_desc.JPG)

https://docs.aws.amazon.com/vpc/latest/userguide/vpc-example-private-subnets-nat.html

### Step 1: VPC creation
![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_VPC_creation.JPG)
As shown above, when you create VPC there are 2 public subnets and 2 private subnets getting created. route table route the traffic to the internet gateway. If no route table, nothing happens in subnet. so public subnet should have route table with internet gateway attached so that outside traffic flows into the subnet.

Elastic IP is called as Static IP address and it never changes. If your instance does not have a public IP address, you can associate an Elastic IP address with your instance to enable communication with the internet.
 ### Step 2 : Auto scaling group creation
 In this project we are using auto scaling group to create EC2 instances.
 
 An Amazon EC2 Auto Scaling Group(ASG) contains a collection of EC2 instances that share similar characteristics and are treated as a logical grouping for the purposes of fleet management and dynamic scaling.
 
 Auto scaling group in AWS cant be created directly, we can use launch template. First create launch template as shown below,
give name for auto scaling group, AMI image, instance type,  key pair, security group with allow rule for ssh port,  
![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_autoscaling_group_inputs.JPG)

 Now create auto scaling group,
 ![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_autoscaling_group_creation.JPG)
 while choosing network, choose VPC that we have created and choose availablity zones and subnets as "us-east1a-private-subnet" & "us-east1b-private-subnet"
 ![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_choose_network.JPG)
and in the next no need to create load balancer in 
 ![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_autoscaling_capacity.JPG)

 Now the EC2 instance is created and it does not have public IP address.  So we need to create bastian or jump host

 create it as shown below as like normal EC2 instance with configuration such as choose the VPC that we created, enable elastic IP address, choose subnet as public subnet, image ubuntu, instance type as micro, 
![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_bastian_host.JPG)

![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_bastian_host_created.JPG)

Now we need to copy the key pair to bastian host and using that we can login to EC2 instance from bastian host.
![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_copy_key_to_bastian_host.JPG)

Now login to bastian host with public ip via ssh, then give 400 permission for key pair file and then login to other EC2 instance with private ip, 
![image](https://github.com/balathecoder/newproject/blob/master/aws/day7_login_to_ec2.JPG)
