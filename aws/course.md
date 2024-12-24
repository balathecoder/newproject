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

