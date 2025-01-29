# Terraform

Terraform is a open source tool that allows users to manage and provision cloud infrastructure using code. It is also known as Infrastructure as Code(IaC).

HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse and share. You can use then a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.

## Project

### Step 1 : Create new user in AWS using IAM
Create vscode user in IAM.
![image](https://github.com/balathecoder/newproject/blob/master/terraform/1_IAM_vscode_user_with_admin_policy.JPG)

### Step 2 : Install AWS Toolkit in VS Code
Go to Extensions -> Install "AWS Toolkit" extension.

### Step 3 : Create AWS Profile,
* In AWS, IAM -> Users,
* select vscode1 user,
* create Access key,
* download Access key,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/2_VS_Code_install_AWS_toolkit.JPG)

### Step 4 : Provide credential in VS Code
* copy username & secret key from AWS download .csv file.
* VS Code -> View -> Terminal -> AWS: Create Credential Profile
* Provide profilename, access key and secret key which are copied from .csv file. As shown below vscode profile with access key & secret key
![image](https://github.com/balathecoder/newproject/blob/master/terraform/2_1_VS_Code_AWS_Profile.JPG)

### Step 5 : Connect to AWS
* In VS Code -> View -> Terminal -> AWS: Connect to AWS
* Now it's connected.
![image](https://github.com/balathecoder/newproject/blob/master/terraform/3_VS_Code_Connect_to_AWS.JPG)

### Step 6 : Install Terraform extension in VS Code

### Step 7 : Open new folder for terraform scripts in VS code

### Step 8 : Build a below dev environment in AWS with Terraform
![image](https://github.com/balathecoder/newproject/blob/master/terraform/4_Build_Dev_Env_With_Terraform.JPG)


Terraform files : providers.tf, variables.tf, main.tf and terraform.tfvars
Configuration files : ssh-config.tpl(based on IP address of Dev node), userdata.tpl with config to bootstrap that node.
AWS Components to create : VPC, Internet Gateway, Public Route table, Public security group, Public subnet, Dev node.

### Step 9 : Use Terraform docs to connect to AWS provider to put it in provider.tf and "terraform init"
Install terraform from here according to OS, I download AMD64 version of terraform for Windows, extracted, copied to "C:\Program Files\terraform" and edited Path env varilable,
https://developer.hashicorp.com/terraform/install

https://registry.terraform.io/providers/hashicorp/aws/latest/docs

Here as a first step create providers.tf in terraform_scripts/ folder,
```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
    }
  }
}

provider "aws" {
  region                   = "us-east-1"
  shared_credentials_files = ["C:/Users/223096933/.aws/credentials"]
  profile                  = "vscode"
}
```
After this, run the "terraform init" as shown below, 

![image](https://github.com/balathecoder/newproject/blob/master/terraform/5_Terraform_init.JPG)

As we saw above, Terraform has been successfully initialized. HashiCorp version v5.84.0. Also it created .terraform.lock.hcl file and .terraform folder.

### Step 10 : Find Resources in Terraform to put resources in main.tf file and "terraform plan"
Go to documentation and find AWS, and then click on Documentation, in left panel search vpc or resource, then choose aws_vpc as shown below,
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc
![image](https://github.com/balathecoder/newproject/blob/master/terraform/6_Terraform_Find_Resources.JPG)

To create VPC put below content in main.tf file,
```
resource "aws_vpc" "mtc_vpc" {
    cidr_block = "10.123.0.0/16"
    enable_dns_hostnames = true
    enable_dns_support = true

    tags = {
        Name = "dev"
    }
}
```
It is also possible to create separate .tf file for every resources. Now run "terraform plan" command as shown below,

Terraform plan : Terraform plan command creates an execution plan, which lets you preview the changes that Terraform plan to make to your infrastructure.

It will show error if there are any issues,
```
PS C:\Users\223096933\Learning\terraform\terraform_scripts> terraform.exe plan
╷
│ Error: expected cidr_block to contain a valid Value, got: 10.123.0.0./16 with err: invalid CIDR address: 10.123.0.0./16
│ 
│   with aws_vpc.mtc_vpc,
│   on main.tf line 2, in resource "aws_vpc" "mtc_vpc":
│    2:     cidr_block = "10.123.0.0./16"
│ 
╵
```

```
> terraform.exe plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_vpc.mtc_vpc will be created
  + resource "aws_vpc" "mtc_vpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "10.123.0.0/16"
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_dns_hostnames                 = true
      + enable_dns_support                   = true
      + enable_network_address_usage_metrics = (known after apply)
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Name" = "dev"
        }
      + tags_all                             = {
          + "Name" = "dev"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

![image](https://github.com/balathecoder/newproject/blob/master/terraform/7_Terraform_plan.JPG)

### Step 11 : terraform apply command,

The terraform apply command executes the action proposed in terraform plan to create, update or destroy infrastructure.

Now run the command, it will prompt for confirmation and type "yes",

```
> terraform.exe apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_vpc.mtc_vpc will be created
  + resource "aws_vpc" "mtc_vpc" {
      + arn                                  = (known after apply)
      + cidr_block                           = "10.123.0.0/16"    
      + default_network_acl_id               = (known after apply)
      + default_route_table_id               = (known after apply)
      + default_security_group_id            = (known after apply)
      + dhcp_options_id                      = (known after apply)
      + enable_dns_hostnames                 = true
      + enable_dns_support                   = true
      + enable_network_address_usage_metrics = (known after apply)
      + id                                   = (known after apply)
      + instance_tenancy                     = "default"
      + ipv6_association_id                  = (known after apply)
      + ipv6_cidr_block                      = (known after apply)
      + ipv6_cidr_block_network_border_group = (known after apply)
      + main_route_table_id                  = (known after apply)
      + owner_id                             = (known after apply)
      + tags                                 = {
          + "Name" = "dev"
        }
      + tags_all                             = {
          + "Name" = "dev"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_vpc.mtc_vpc: Creating...
aws_vpc.mtc_vpc: Still creating... [10s elapsed]
aws_vpc.mtc_vpc: Creation complete after 14s [id=vpc-07f9c76fea66f7958]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
PS C:\Users\223096933\Learning\terraform\terraform_scripts> 
```

Login to AWS, in us-east-1 region, check that the VPC with name "dev" is created,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/7_Terraform_apply.JPG)

Connect to AWS from VS code, Under Resources folder in "us-east-1", check that VPC is listed,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/7_Terraform_apply.JPG)

### Terraform state file
Terraform state file is a json file that stores information about the resources that Terraform manages.It's a snapshot of the current state of the infrastructure. The state is stored in a local file named terraform.tfstate

![image](https://github.com/balathecoder/newproject/blob/master/terraform/9_Terraform_state_file.JPG)

### terraform state command
terraform state list to list the resources created by terraform that are referred in .tfstate file,
```
> terraform.exe state list
aws_vpc.mtc_vpc
```

terraform state show to list resource content,
```
> terraform.exe state show aws_vpc.mtc_vpc
# aws_vpc.mtc_vpc:
resource "aws_vpc" "mtc_vpc" {
    arn                                  = "arn:aws:ec2:us-east-1:381491823509:vpc/vpc-07f9c76fea66f7958"
    assign_generated_ipv6_cidr_block     = false
    cidr_block                           = "10.123.0.0/16"
    default_network_acl_id               = "acl-00d9fe7a34ff95e74"
    default_route_table_id               = "rtb-0bba1d83bc4dd46d2"
    default_security_group_id            = "sg-04fcadf2352447a37"
    dhcp_options_id                      = "dopt-0a4cbdf7b65ab37ae"
    enable_dns_hostnames                 = true
    enable_dns_support                   = true
    enable_network_address_usage_metrics = false
    id                                   = "vpc-07f9c76fea66f7958"
    instance_tenancy                     = "default"
    ipv6_association_id                  = null
    ipv6_cidr_block                      = null
    ipv6_cidr_block_network_border_group = null
    ipv6_ipam_pool_id                    = null
    ipv6_netmask_length                  = 0
    main_route_table_id                  = "rtb-0bba1d83bc4dd46d2"
    owner_id                             = "381491823509"
    tags                                 = {
        "Name" = "dev"
    }
    tags_all                             = {
        "Name" = "dev"
    }
}
```

### terraform show command
Terraform show is used to provide a human readable output from a state or plan file. This can be used to inspect a plan to ensure that the planned operations are expected, or to inspect the current state as Terraform sees it.
```
> terraform.exe show 
# aws_vpc.mtc_vpc:
resource "aws_vpc" "mtc_vpc" {
    arn                                  = "arn:aws:ec2:us-east-1:381491823509:vpc/vpc-07f9c76fea66f7958"
    assign_generated_ipv6_cidr_block     = false
    cidr_block                           = "10.123.0.0/16"
    default_network_acl_id               = "acl-00d9fe7a34ff95e74"
    default_route_table_id               = "rtb-0bba1d83bc4dd46d2"
    default_security_group_id            = "sg-04fcadf2352447a37"
    dhcp_options_id                      = "dopt-0a4cbdf7b65ab37ae"
    enable_dns_hostnames                 = true
    enable_dns_support                   = true
    enable_network_address_usage_metrics = false
    id                                   = "vpc-07f9c76fea66f7958"
    instance_tenancy                     = "default"
    ipv6_association_id                  = null
    ipv6_cidr_block                      = null
    ipv6_cidr_block_network_border_group = null
    ipv6_ipam_pool_id                    = null
    ipv6_netmask_length                  = 0
    main_route_table_id                  = "rtb-0bba1d83bc4dd46d2"
    owner_id                             = "381491823509"
    tags                                 = {
        "Name" = "dev"
    }
    tags_all                             = {
        "Name" = "dev"
    }
}
```

### terraform destroy

Terrafirn destroy command is a convenient way to destroy all remote objects managed by a particular terraform configuration.

terraform plan -destroy command show you the proposed destroy changes without executing them.

```
> terraform.exe plan -destroy
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-07f9c76fea66f7958]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # aws_vpc.mtc_vpc will be destroyed
  - resource "aws_vpc" "mtc_vpc" {
      - arn                                  = "arn:aws:ec2:us-east-1:381491823509:vpc/vpc-07f9c76fea66f7958" -> null
      - assign_generated_ipv6_cidr_block     = false -> null
      - cidr_block                           = "10.123.0.0/16" -> null
      - default_network_acl_id               = "acl-00d9fe7a34ff95e74" -> null
      - default_route_table_id               = "rtb-0bba1d83bc4dd46d2" -> null
      - default_security_group_id            = "sg-04fcadf2352447a37" -> null
      - dhcp_options_id                      = "dopt-0a4cbdf7b65ab37ae" -> null
      - enable_dns_hostnames                 = true -> null
      - enable_dns_support                   = true -> null
      - enable_network_address_usage_metrics = false -> null
      - id                                   = "vpc-07f9c76fea66f7958" -> null
      - instance_tenancy                     = "default" -> null
      - ipv6_netmask_length                  = 0 -> null
      - main_route_table_id                  = "rtb-0bba1d83bc4dd46d2" -> null
      - owner_id                             = "381491823509" -> null
      - tags                                 = {
          - "Name" = "dev"
        } -> null
      - tags_all                             = {
          - "Name" = "dev"
        } -> null
        # (4 unchanged attributes hidden)
    }

Plan: 0 to add, 0 to change, 1 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform    
apply" now.
```

now run terraform destroy command,
```
> terraform.exe destroy      
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-07f9c76fea66f7958]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:  
  - destroy

Terraform will perform the following actions:

  # aws_vpc.mtc_vpc will be destroyed
  - resource "aws_vpc" "mtc_vpc" {
      - arn                                  = "arn:aws:ec2:us-east-1:381491823509:vpc/vpc-07f9c76fea66f7958" -> null
      - assign_generated_ipv6_cidr_block     = false -> null
      - cidr_block                           = "10.123.0.0/16" -> null
      - default_network_acl_id               = "acl-00d9fe7a34ff95e74" -> null
      - default_route_table_id               = "rtb-0bba1d83bc4dd46d2" -> null
      - default_security_group_id            = "sg-04fcadf2352447a37" -> null
      - dhcp_options_id                      = "dopt-0a4cbdf7b65ab37ae" -> null
      - enable_dns_hostnames                 = true -> null
      - enable_dns_support                   = true -> null
      - enable_network_address_usage_metrics = false -> null
      - id                                   = "vpc-07f9c76fea66f7958" -> null
      - instance_tenancy                     = "default" -> null
      - ipv6_netmask_length                  = 0 -> null
      - main_route_table_id                  = "rtb-0bba1d83bc4dd46d2" -> null
      - owner_id                             = "381491823509" -> null
      - tags                                 = {
          - "Name" = "dev"
        } -> null
      - tags_all                             = {
          - "Name" = "dev"
        } -> null
        # (4 unchanged attributes hidden)
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_vpc.mtc_vpc: Destroying... [id=vpc-07f9c76fea66f7958]
aws_vpc.mtc_vpc: Destruction complete after 1s

Destroy complete! Resources: 1 destroyed.
```
Note : `terraform destroy -auto-approve` to run without prompting for confirmation. Also -uato-approve is available with `terraform apply -auto-approve` 
To confirm the same run terraform show,
```
> terraform.exe show   
The state file is empty. No resources are represented.
```

### Step 12: EC2 instances needs public IP and so public subnet is a pre-requisite in VPC

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/subnet

Created VPC does not have any subnet,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/11_VPC_with_no_subnets.JPG)

Need to have public IP in EC2 instance so that it could be accessible from outside and for that we need to have public subnet.

Note : resources  need not to have a quotation in terraform script. For example aws_vpc.mtc_vpc is a resource so when you mention this as vpc_id, do not enclose with double quotes.


```
> terraform.exe state show  aws_vpc.mtc_vpc
# aws_vpc.mtc_vpc:
resource "aws_vpc" "mtc_vpc" {
    arn                                  = "arn:aws:ec2:us-east-1:381491823509:vpc/vpc-0fd349a2abcf6ceca"
    assign_generated_ipv6_cidr_block     = false
    cidr_block                           = "10.123.0.0/16"
    default_network_acl_id               = "acl-025672fa436273219"
    default_route_table_id               = "rtb-0b8dc93cb91d43550"
    default_security_group_id            = "sg-0756fc5d904b597f3"
    dhcp_options_id                      = "dopt-0a4cbdf7b65ab37ae"
    enable_dns_hostnames                 = true
    enable_dns_support                   = true
    enable_network_address_usage_metrics = false
    id                                   = "vpc-0fd349a2abcf6ceca"
    instance_tenancy                     = "default"
    ipv6_association_id                  = null
    ipv6_cidr_block                      = null
    ipv6_cidr_block_network_border_group = null
    ipv6_ipam_pool_id                    = null
    ipv6_netmask_length                  = 0
    main_route_table_id                  = "rtb-0b8dc93cb91d43550"
    owner_id                             = "381491823509"
    tags                                 = {
        "Name" = "dev"
    }
    tags_all                             = {
        "Name" = "dev"
    }
}
```
As shown above, aws_vpc.mtc_vpc.id field is required for subnet section.

After adding aws_subnet resource in main.tf,
```
resource "aws_vpc" "mtc_vpc" {
    cidr_block = "10.123.0.0/16"
    enable_dns_hostnames = true
    enable_dns_support = true

    tags = {
        Name = "dev"
    }
}

resource "aws_subnet" "mtc_public_subnet" {
    # need to provide vpc id 
    vpc_id = aws_vpc.mtc_vpc.id
    # this is one of the CIDR block range in the VPC
    cidr_block = "10.123.1.0/24"
    map_public_ip_on_launch = true
    availability_zone = "us-east-1a"
    tags = {
        Name = "dev-public"
    }
}
```

Now run terraform plan command which shows what additional resources will be created,
```
> terraform.exe plan
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0fd349a2abcf6ceca]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:  
  + create

Terraform will perform the following actions:

  # aws_subnet.mtc_public_subnet will be created
  + resource "aws_subnet" "mtc_public_subnet" {
      + arn                                            = (known after apply)
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-east-1a"
      + availability_zone_id                           = (known after apply)
      + cidr_block                                     = "10.123.1.0/24"
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)
      + ipv6_cidr_block_association_id                 = (known after apply)
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = true
      + owner_id                                       = (known after apply)
      + private_dns_hostname_type_on_launch            = (known after apply)
      + tags                                           = {
          + "Name" = "dev-public"
        }
      + tags_all                                       = {
          + "Name" = "dev-public"
        }
      + vpc_id                                         = "vpc-0fd349a2abcf6ceca"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
 to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform
apply" now.
```

Public subnet is created after running "terraform apply -auto-approve",
```
> terraform.exe apply -auto-approve
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0fd349a2abcf6ceca]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_subnet.mtc_public_subnet will be created
  + resource "aws_subnet" "mtc_public_subnet" {
      + arn                                            = (known after apply)    
      + assign_ipv6_address_on_creation                = false
      + availability_zone                              = "us-east-1a"
      + availability_zone_id                           = (known after apply)    
      + cidr_block                                     = "10.123.1.0/24"        
      + enable_dns64                                   = false
      + enable_resource_name_dns_a_record_on_launch    = false
      + enable_resource_name_dns_aaaa_record_on_launch = false
      + id                                             = (known after apply)    
      + ipv6_cidr_block_association_id                 = (known after apply)    
      + ipv6_native                                    = false
      + map_public_ip_on_launch                        = true
      + owner_id                                       = (known after apply)    
      + private_dns_hostname_type_on_launch            = (known after apply)    
      + tags                                           = {
          + "Name" = "dev-public"
        }
      + tags_all                                       = {
          + "Name" = "dev-public"
        }
      + vpc_id                                         = "vpc-0fd349a2abcf6ceca"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
aws_subnet.mtc_public_subnet: Creating...
aws_subnet.mtc_public_subnet: Still creating... [10s elapsed]
aws_subnet.mtc_public_subnet: Creation complete after 13s [id=subnet-076b8139405fca8d6]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```


"terraform state list" shows both VPC and subnets as shown below,
```
> terraform.exe state list
aws_subnet.mtc_public_subnet
aws_vpc.mtc_vpc
```

"terraform show" gives resource properties,
```
> terraform.exe show      
# aws_subnet.mtc_public_subnet:
resource "aws_subnet" "mtc_public_subnet" {
    arn                                            = "arn:aws:ec2:us-east-1:381491823509:subnet/subnet-076b8139405fca8d6"
    assign_ipv6_address_on_creation                = false
    availability_zone                              = "us-east-1a"
    availability_zone_id                           = "use1-az6"
    cidr_block                                     = "10.123.1.0/24"
    customer_owned_ipv4_pool                       = null
    enable_dns64                                   = false
    enable_lni_at_device_index                     = 0
    enable_resource_name_dns_a_record_on_launch    = false
    enable_resource_name_dns_aaaa_record_on_launch = false
    id                                             = "subnet-076b8139405fca8d6"
    ipv6_cidr_block                                = null
    ipv6_cidr_block_association_id                 = null
    ipv6_native                                    = false
    map_customer_owned_ip_on_launch                = false
    map_public_ip_on_launch                        = true
    outpost_arn                                    = null
    owner_id                                       = "381491823509"
    private_dns_hostname_type_on_launch            = "ip-name"
    tags                                           = {
        "Name" = "dev-public"
    }
    tags_all                                       = {
        "Name" = "dev-public"
    }
    vpc_id                                         = "vpc-0fd349a2abcf6ceca"
}

# aws_vpc.mtc_vpc:
resource "aws_vpc" "mtc_vpc" {
    arn                                  = "arn:aws:ec2:us-east-1:381491823509:vpc/vpc-0fd349a2abcf6ceca"
    assign_generated_ipv6_cidr_block     = false
    cidr_block                           = "10.123.0.0/16"
    default_network_acl_id               = "acl-025672fa436273219"
    default_route_table_id               = "rtb-0b8dc93cb91d43550"
    default_security_group_id            = "sg-0756fc5d904b597f3"
    dhcp_options_id                      = "dopt-0a4cbdf7b65ab37ae"
    enable_dns_hostnames                 = true
    enable_dns_support                   = true
    enable_network_address_usage_metrics = false
    id                                   = "vpc-0fd349a2abcf6ceca"
    instance_tenancy                     = "default"
    ipv6_association_id                  = null
    ipv6_cidr_block                      = null
    ipv6_cidr_block_network_border_group = null
    ipv6_ipam_pool_id                    = null
    ipv6_netmask_length                  = 0
    main_route_table_id                  = "rtb-0b8dc93cb91d43550"
    owner_id                             = "381491823509"
    tags                                 = {
        "Name" = "dev"
    }
    tags_all                             = {
        "Name" = "dev"
    }
}
```
After subnet creation, the VPC in AWS
![image](https://github.com/balathecoder/newproject/blob/master/terraform/12_VPC_with_public_subnet.JPG)

### Step 13 : With Internet Gateway
Internet gateway component horizontally scaled, redundant, and highly available VPC component that allows communication between VPC and Internet.

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/internet_gateway

Add below content to main.tf for Internet gateway,
```
resource "aws_internet_gateway" "mtc_internet_gateway" {
    vpc_id = aws_vpc.mtc_vpc.id
    tags = {
        Name = "dev-igw"
    }
}
```

"terraform fmt" command is used to rewrite terraform configuration files to a canonical format and style. 
```
> terraform.exe fmt
main.tf
providers.tf
```

main.tf content,
```
resource "aws_vpc" "mtc_vpc" {
    cidr_block = "10.123.0.0/16"
    enable_dns_hostnames = true
    enable_dns_support = true

    tags = {
        Name = "dev"
    }
}

resource "aws_subnet" "mtc_public_subnet" {
    # need to provide vpc id 
    vpc_id = aws_vpc.mtc_vpc.id
    # this is one of the CIDR block range in the VPC
    cidr_block = "10.123.1.0/24"
    map_public_ip_on_launch = true
    availability_zone = "us-east-1a"
    tags = {
        Name = "dev-public"
    }
}

resource "aws_internet_gateway" "mtc_internet_gateway" {
    vpc_id = aws_vpc.mtc_vpc.id
    tags = {
        Name = "dev-igw"
    }
}
```

Now run "terraform plan" command as,

```
> terraform.exe plan
╷
│ Error: Invalid resource type
│ 
│   on main.tf line 23, in resource "aws_internet_geatway" "mtc_internet_gateway":
│   23: resource "aws_internet_geatway" "mtc_internet_gateway" {
│ 
│ The provider hashicorp/aws does not support resource type "aws_internet_geatway". Did you mean "aws_internet_gateway"?
╵
PS C:\Users\223096933\Learning\terraform\terraform_scripts> terraform.exe plan
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0fd349a2abcf6ceca]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-076b8139405fca8d6]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_internet_gateway.mtc_internet_gateway will be created
  + resource "aws_internet_gateway" "mtc_internet_gateway" {
      + arn      = (known after apply)
      + id       = (known after apply)
      + owner_id = (known after apply)
      + tags     = {
          + "Name" = "dev-igw"
        }
      + tags_all = {
          + "Name" = "dev-igw"
        }
      + vpc_id   = "vpc-0fd349a2abcf6ceca"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform    
apply" now.
```

Run "terraform apply" to create resources,
```
> terraform.exe apply -auto-approve
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0fd349a2abcf6ceca]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-076b8139405fca8d6]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_internet_gateway.mtc_internet_gateway will be created
  + resource "aws_internet_gateway" "mtc_internet_gateway" { 
      + arn      = (known after apply)
      + id       = (known after apply)
      + owner_id = (known after apply)
      + tags     = {
          + "Name" = "dev-igw"
        }
      + tags_all = {
          + "Name" = "dev-igw"
        }
      + vpc_id   = "vpc-0fd349a2abcf6ceca"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
aws_internet_gateway.mtc_internet_gateway: Creating...
aws_internet_gateway.mtc_internet_gateway: Creation complete after 2s [id=igw-0fc66ee7dce638904]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

AWS Toolkit in VS Code shows that Internet Gateway,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/13_AWS_Toolkit_with_IGW.JPG)

### Step 14 : Route and Route table
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route

A route is a single rule within a route table that defines where network traffic should be directed based on a destination IP address, while a "route table" is a collection of these routes, essentially acting as a map that determines how network traffic flows within your VPC by specifying which target to send traffic to for a given destination IP range.

```
resource "aws_route_table" "mtc_public_rt" {
    vpc_id = aws_vpc.ntc_vpc.id

    tags = {
        Name=  "dev_public_rt"
    }
  
}

resource "aws_route" "default_route" {
    route_table_id = aws_route_table.mtc_public_rt.id
    destination_cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.mtc_internet_gateway.id
}
```

Run "terraform plan" as shown below,
```
> terraform.exe plan
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0fd349a2abcf6ceca]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-0fc66ee7dce638904]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-076b8139405fca8d6]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with
the following symbols:
  + create

Terraform will perform the following actions:

  # aws_route.default_route will be created  
  + resource "aws_route" "default_route" {
      + destination_cidr_block = "0.0.0.0/0"
      + gateway_id             = "igw-0fc66ee7dce638904"
      + id                     = (known after apply)
      + instance_id            = (known after apply)
      + instance_owner_id      = (known after apply)
      + network_interface_id   = (known after apply)
      + origin                 = (known after apply)
      + route_table_id         = (known after apply)
      + state                  = (known after apply)
    }

  # aws_route_table.mtc_public_rt will be created
  + resource "aws_route_table" "mtc_public_rt" {
      + arn              = (known after apply)
      + id               = (known after apply)
      + owner_id         = (known after apply)
      + propagating_vgws = (known after apply)
      + route            = (known after apply)
      + tags             = {
          + "Name" = "dev_public_rt"
        }
      + tags_all         = {
          + "Name" = "dev_public_rt"
        }
      + vpc_id           = "vpc-0fd349a2abcf6ceca"
    }

Plan: 2 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if 
you run "terraform apply" now.
```


Run "terraform apply" as shown below,
```
> terraform.exe apply -auto-approve
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0fd349a2abcf6ceca]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-0fc66ee7dce638904]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-076b8139405fca8d6]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_route.default_route will be created
  + resource "aws_route" "default_route" {
      + destination_cidr_block = "0.0.0.0/0"
      + gateway_id             = "igw-0fc66ee7dce638904"
      + id                     = (known after apply)    
      + instance_id            = (known after apply)    
      + instance_owner_id      = (known after apply)    
      + network_interface_id   = (known after apply)    
      + origin                 = (known after apply)    
      + route_table_id         = (known after apply)    
      + state                  = (known after apply)    
    }

  # aws_route_table.mtc_public_rt will be created       
  + resource "aws_route_table" "mtc_public_rt" {
      + arn              = (known after apply)
      + id               = (known after apply)
      + owner_id         = (known after apply)
      + propagating_vgws = (known after apply)
      + route            = (known after apply)
      + tags             = {
          + "Name" = "dev_public_rt"
        }
      + tags_all         = {
          + "Name" = "dev_public_rt"
        }
      + vpc_id           = "vpc-0fd349a2abcf6ceca"
    }

Plan: 2 to add, 0 to change, 0 to destroy.
aws_route_table.mtc_public_rt: Creating...
aws_route_table.mtc_public_rt: Creation complete after 3s [id=rtb-0583a6102086d406c]
aws_route.default_route: Creating...
aws_route.default_route: Creation complete after 2s [id=r-rtb-0583a6102086d406c1080289494]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

List the resources created,
```
> terraform.exe state list
aws_internet_gateway.mtc_internet_gateway
aws_route.default_route
aws_route_table.mtc_public_rt
aws_subnet.mtc_public_subnet
aws_vpc.mtc_vpc
```

### Step 15 : Associate Subnet to Route table

By default mtc_public_subnet subnet gets associated with default route table, now we need to associate that with the route table we created mtc_public_rt

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/route_table_association

![image](https://github.com/balathecoder/newproject/blob/master/terraform/14_Subnet_association_to_Route_table_in_VPC.JPG)
```
resource "aws_route_table_association" "mtc_public_assoc" {
    subnet_id = aws_subnet.mtc_public_subnet.id
    route_table_id = aws_route_table.mtc_public_rt.id
}
```

Run terraform plan now,
```
> terraform.exe plan 
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # aws_route_table_association.mtc_public_assoc will be created
  + resource "aws_route_table_association" "mtc_public_assoc" {
      + id             = (known after apply)
      + route_table_id = "rtb-06bf62c4b0084a99f"
      + subnet_id      = "subnet-02e676bc5ba621e8c"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run    
"terraform apply" now.
PS C:\Users\223096933\Learning\terraform\terraform_scripts> 
```

Run terraform apply
```
> terraform.exe apply -auto-approve
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # aws_route_table_association.mtc_public_assoc will be created
  + resource "aws_route_table_association" "mtc_public_assoc" {
      + id             = (known after apply)
      + route_table_id = "rtb-06bf62c4b0084a99f"
      + subnet_id      = "subnet-02e676bc5ba621e8c"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
aws_route_table_association.mtc_public_assoc: Creating...
aws_route_table_association.mtc_public_assoc: Creation complete after 2s [id=rtbassoc-021f6333d315913d1]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
PS C:\Users\223096933\Learning\terraform\terraform_scripts> 
```
![image](https://github.com/balathecoder/newproject/blob/master/terraform/15_Subnet_associated_to_Route_table_in_VPC.JPG)

### Step 16  : Security group
Security group is a virtual firewall that controls traffic to and from EC2 instances. 

In below egress allows everything(all ports+protocol), -1 in protocol represents all protocol including udp,tcp,icmp, etc
ingress allows only port 443.

```
resource "aws_security_group" "mtc_sg" {
    name = "dev_sg"
    description = "dev security group"
    vpc_id = aws_vpc.mtc_vpc.id

    # from external to resources in VPC
    ingress {
        from_port = 0
        to_port = 0
        protocol = "-1"
        # put your EC2 instance IP address here.
        cidr_blocks = ["0.0.0.0/0"]
    }

    # from resources in VPC to external.
    egress {
        from_port = 0
        to_port = 0
        protocol = "-1"
        cidr_blocks = ["0.0.0.0/0"]
    }
}
```

Run "terraform plan" as shown below,
```
> terraform.exe plan
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]
aws_route_table_association.mtc_public_assoc: Refreshing state... [id=rtbassoc-021f6333d315913d1]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # aws_security_group.mtc_sg will be created
  + resource "aws_security_group" "mtc_sg" {
      + arn                    = (known after apply)
      + description            = "dev security group"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + name                   = "dev_sg"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = "vpc-0e61e191741ac2ba1"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run    
"terraform apply" now.
```

Run "terraform apply" as shown below,
```
> terraform.exe apply
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_route_table_association.mtc_public_assoc: Refreshing state... [id=rtbassoc-021f6333d315913d1]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # aws_security_group.mtc_sg will be created
  + resource "aws_security_group" "mtc_sg" {
      + arn                    = (known after apply)
      + description            = "dev security group"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + from_port        = 0
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "-1"
              + security_groups  = []
              + self             = false
              + to_port          = 0
                # (1 unchanged attribute hidden)
            },
        ]
      + name                   = "dev_sg"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = "vpc-0e61e191741ac2ba1"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_security_group.mtc_sg: Creating...
aws_security_group.mtc_sg: Creation complete after 7s [id=sg-0c5cda6e9ba0736ac]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Ingress,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/16_Security_Group_Ingress.JPG)
Egress,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/17_Security_Group_Egress.JPG)

### Step 17 : AMI

AMI stands for Amazon Machine Image, which is a template used to create virtual servers in Amazon web services. AMIs are preconfigured virtual machines which contain an operating system and other softwares.

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ami

Data source(datasources.tf) in terraform allow you to fetch information from your cloud provider and use it within your configuration. This can be particularly useful for validating inputs, such as checking if a specific Amazon Machine Image ID exists in a given region before creating an EC2 instance.

![image](https://github.com/balathecoder/newproject/blob/master/terraform/18_Image_in_Data_Source.JPG)

Need to search and find image AMI id with datasources.tf,

```
data "aws_ami" "server_ami" {
    most_recent = true
    owners = ["099720109477"]

    filter {
        name = "name"
        values = ["ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-*"]
    }
}
```

Run "terraform apply" command,
```
> terraform.exe apply -auto-approve
data.aws_ami.server_ami: Reading...
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
data.aws_ami.server_ami: Read complete after 1s [id=ami-0e1bed4f06a3b463d]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_security_group.mtc_sg: Refreshing state... [id=sg-0c5cda6e9ba0736ac]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]
aws_route_table_association.mtc_public_assoc: Refreshing state... [id=rtbassoc-021f6333d315913d1]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.
```

Check the terraform.tfstate about the image available in the availability zone where we are creating our resources terraform.tfstate,
```
{
  "version": 4,
  "terraform_version": "1.10.5",
  "serial": 29,
  "lineage": "267946f3-8951-98eb-b8b1-50e9b12ecaee",
  "outputs": {},
  "resources": [
    {
      "mode": "data",
      "type": "aws_ami",
      "name": "server_ami",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "architecture": "x86_64",
            "arn": "arn:aws:ec2:us-east-1::image/ami-0e1bed4f06a3b463d",
            "block_device_mappings": [
              {
                "device_name": "/dev/sda1",
                "ebs": {
                  "delete_on_termination": "true",
                  "encrypted": "false",
                  "iops": "0",
                  "snapshot_id": "snap-0b9170dee60c9ec7a",
                  "throughput": "0",
                  "volume_size": "8",
                  "volume_type": "gp2"
                },
                "no_device": "",
                "virtual_name": ""
              },
              {
                "device_name": "/dev/sdb",
                "ebs": {},
                "no_device": "",
                "virtual_name": "ephemeral0"
              },
              {
                "device_name": "/dev/sdc",
                "ebs": {},
                "no_device": "",
                "virtual_name": "ephemeral1"
              }
            ],
            "boot_mode": "uefi-preferred",
            "creation_date": "2025-01-12T04:14:49.000Z",
            "deprecation_time": "2027-01-12T04:14:49.000Z",
            "description": "Canonical, Ubuntu, 22.04, amd64 jammy image",
            "ena_support": true,
            "executable_users": null,
            "filter": [
              {
                "name": "name",
                "values": [
                  "ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-*"
                ]
              }
            ],
            "hypervisor": "xen",
            "id": "ami-0e1bed4f06a3b463d",
            "image_id": "ami-0e1bed4f06a3b463d",
            "image_location": "amazon/ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-20250112",
            "image_owner_alias": "amazon",
            "image_type": "machine",
            "imds_support": "",
            "include_deprecated": false,
            "kernel_id": "",
            "most_recent": true,
            "name": "ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-20250112",
            "name_regex": null,
            "owner_id": "099720109477",
            "owners": [
              "099720109477"
            ],
            "platform": "",
            "platform_details": "Linux/UNIX",
            "product_codes": [],
            "public": true,
            "ramdisk_id": "",
            "root_device_name": "/dev/sda1",
            "root_device_type": "ebs",
            "root_snapshot_id": "snap-0b9170dee60c9ec7a",
            "sriov_net_support": "simple",
            "state": "available",
            "state_reason": {
              "code": "UNSET",
              "message": "UNSET"
            },
            "tags": {},
            "timeouts": null,
            "tpm_support": "",
            "uefi_data": null,
            "usage_operation": "RunInstances",
            "virtualization_type": "hvm"
          },
          "sensitive_attributes": []
        }
      ]
    },
    {
      "mode": "managed",
      "type": "aws_internet_gateway",
      "name": "mtc_internet_gateway",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "arn": "arn:aws:ec2:us-east-1:381491823509:internet-gateway/igw-03f5acba6ece22e70",
            "id": "igw-03f5acba6ece22e70",
            "owner_id": "381491823509",
            "tags": {
              "Name": "dev-igw"
            },
            "tags_all": {
              "Name": "dev-igw"
            },
            "timeouts": null,
            "vpc_id": "vpc-0e61e191741ac2ba1"
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjoxMjAwMDAwMDAwMDAwLCJkZWxldGUiOjEyMDAwMDAwMDAwMDAsInVwZGF0ZSI6MTIwMDAwMDAwMDAwMH19",
          "dependencies": [
            "aws_vpc.mtc_vpc"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "aws_route",
      "name": "default_route",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "carrier_gateway_id": "",
            "core_network_arn": "",
            "destination_cidr_block": "0.0.0.0/0",
            "destination_ipv6_cidr_block": "",
            "destination_prefix_list_id": "",
            "egress_only_gateway_id": "",
            "gateway_id": "igw-03f5acba6ece22e70",
            "id": "r-rtb-06bf62c4b0084a99f1080289494",
            "instance_id": "",
            "instance_owner_id": "",
            "local_gateway_id": "",
            "nat_gateway_id": "",
            "network_interface_id": "",
            "origin": "CreateRoute",
            "route_table_id": "rtb-06bf62c4b0084a99f",
            "state": "active",
            "timeouts": null,
            "transit_gateway_id": "",
            "vpc_endpoint_id": "",
            "vpc_peering_connection_id": ""
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjozMDAwMDAwMDAwMDAsImRlbGV0ZSI6MzAwMDAwMDAwMDAwLCJ1cGRhdGUiOjEyMDAwMDAwMDAwMH19",
          "dependencies": [
            "aws_internet_gateway.mtc_internet_gateway",
            "aws_route_table.mtc_public_rt",
            "aws_vpc.mtc_vpc"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "aws_route_table",
      "name": "mtc_public_rt",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "arn": "arn:aws:ec2:us-east-1:381491823509:route-table/rtb-06bf62c4b0084a99f",
            "id": "rtb-06bf62c4b0084a99f",
            "owner_id": "381491823509",
            "propagating_vgws": [],
            "route": [
              {
                "carrier_gateway_id": "",
                "cidr_block": "0.0.0.0/0",
                "core_network_arn": "",
                "destination_prefix_list_id": "",
                "egress_only_gateway_id": "",
                "gateway_id": "igw-03f5acba6ece22e70",
                "ipv6_cidr_block": "",
                "local_gateway_id": "",
                "nat_gateway_id": "",
                "network_interface_id": "",
                "transit_gateway_id": "",
                "vpc_endpoint_id": "",
                "vpc_peering_connection_id": ""
              }
            ],
            "tags": {
              "Name": "dev_public_rt"
            },
            "tags_all": {
              "Name": "dev_public_rt"
            },
            "timeouts": null,
            "vpc_id": "vpc-0e61e191741ac2ba1"
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjozMDAwMDAwMDAwMDAsImRlbGV0ZSI6MzAwMDAwMDAwMDAwLCJ1cGRhdGUiOjEyMDAwMDAwMDAwMH19",
          "dependencies": [
            "aws_vpc.mtc_vpc"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "aws_route_table_association",
      "name": "mtc_public_assoc",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 0,
          "attributes": {
            "gateway_id": "",
            "id": "rtbassoc-021f6333d315913d1",
            "route_table_id": "rtb-06bf62c4b0084a99f",
            "subnet_id": "subnet-02e676bc5ba621e8c",
            "timeouts": null
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjozMDAwMDAwMDAwMDAsImRlbGV0ZSI6MzAwMDAwMDAwMDAwLCJ1cGRhdGUiOjEyMDAwMDAwMDAwMH19",
          "dependencies": [
            "aws_route_table.mtc_public_rt",
            "aws_subnet.mtc_public_subnet",
            "aws_vpc.mtc_vpc"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "aws_security_group",
      "name": "mtc_sg",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 1,
          "attributes": {
            "arn": "arn:aws:ec2:us-east-1:381491823509:security-group/sg-0c5cda6e9ba0736ac",
            "description": "dev security group",
            "egress": [
              {
                "cidr_blocks": [
                  "0.0.0.0/0"
                ],
                "description": "",
                "from_port": 0,
                "ipv6_cidr_blocks": [],
                "prefix_list_ids": [],
                "protocol": "-1",
                "security_groups": [],
                "self": false,
                "to_port": 0
              }
            ],
            "id": "sg-0c5cda6e9ba0736ac",
            "ingress": [
              {
                "cidr_blocks": [
                  "0.0.0.0/0"
                ],
                "description": "",
                "from_port": 0,
                "ipv6_cidr_blocks": [],
                "prefix_list_ids": [],
                "protocol": "-1",
                "security_groups": [],
                "self": false,
                "to_port": 0
              }
            ],
            "name": "dev_sg",
            "name_prefix": "",
            "owner_id": "381491823509",
            "revoke_rules_on_delete": false,
            "tags": {},
            "tags_all": {},
            "timeouts": null,
            "vpc_id": "vpc-0e61e191741ac2ba1"
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjo2MDAwMDAwMDAwMDAsImRlbGV0ZSI6OTAwMDAwMDAwMDAwfSwic2NoZW1hX3ZlcnNpb24iOiIxIn0=",
          "dependencies": [
            "aws_vpc.mtc_vpc"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "aws_subnet",
      "name": "mtc_public_subnet",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 1,
          "attributes": {
            "arn": "arn:aws:ec2:us-east-1:381491823509:subnet/subnet-02e676bc5ba621e8c",
            "assign_ipv6_address_on_creation": false,
            "availability_zone": "us-east-1a",
            "availability_zone_id": "use1-az6",
            "cidr_block": "10.123.1.0/24",
            "customer_owned_ipv4_pool": "",
            "enable_dns64": false,
            "enable_lni_at_device_index": 0,
            "enable_resource_name_dns_a_record_on_launch": false,
            "enable_resource_name_dns_aaaa_record_on_launch": false,
            "id": "subnet-02e676bc5ba621e8c",
            "ipv6_cidr_block": "",
            "ipv6_cidr_block_association_id": "",
            "ipv6_native": false,
            "map_customer_owned_ip_on_launch": false,
            "map_public_ip_on_launch": true,
            "outpost_arn": "",
            "owner_id": "381491823509",
            "private_dns_hostname_type_on_launch": "ip-name",
            "tags": {
              "Name": "dev-public"
            },
            "tags_all": {
              "Name": "dev-public"
            },
            "timeouts": null,
            "vpc_id": "vpc-0e61e191741ac2ba1"
          },
          "sensitive_attributes": [],
          "private": "eyJlMmJmYjczMC1lY2FhLTExZTYtOGY4OC0zNDM2M2JjN2M0YzAiOnsiY3JlYXRlIjo2MDAwMDAwMDAwMDAsImRlbGV0ZSI6MTIwMDAwMDAwMDAwMH0sInNjaGVtYV92ZXJzaW9uIjoiMSJ9",
          "dependencies": [
            "aws_vpc.mtc_vpc"
          ]
        }
      ]
    },
    {
      "mode": "managed",
      "type": "aws_vpc",
      "name": "mtc_vpc",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "schema_version": 1,
          "attributes": {
            "arn": "arn:aws:ec2:us-east-1:381491823509:vpc/vpc-0e61e191741ac2ba1",
            "assign_generated_ipv6_cidr_block": false,
            "cidr_block": "10.123.0.0/16",
            "default_network_acl_id": "acl-04e4417ef47b6a0a2",
            "default_route_table_id": "rtb-0fc5a860044aaca14",
            "default_security_group_id": "sg-0ed75616ac6bf0f0d",
            "dhcp_options_id": "dopt-0a4cbdf7b65ab37ae",
            "enable_dns_hostnames": true,
            "enable_dns_support": true,
            "enable_network_address_usage_metrics": false,
            "id": "vpc-0e61e191741ac2ba1",
            "instance_tenancy": "default",
            "ipv4_ipam_pool_id": null,
            "ipv4_netmask_length": null,
            "ipv6_association_id": "",
            "ipv6_cidr_block": "",
            "ipv6_cidr_block_network_border_group": "",
            "ipv6_ipam_pool_id": "",
            "ipv6_netmask_length": 0,
            "main_route_table_id": "rtb-0fc5a860044aaca14",
            "owner_id": "381491823509",
            "tags": {
              "Name": "dev"
            },
            "tags_all": {
              "Name": "dev"
            }
          },
          "sensitive_attributes": [],
          "private": "eyJzY2hlbWFfdmVyc2lvbiI6IjEifQ=="
        }
      ]
    }
  ],
  "check_results": null
}

```

### Step 18: Key pair
This key pair will be used by the EC2 instance that we are going to create and it will be used for ssh into it.

Create key pair,
```
> ssh-keygen.exe -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (C:\Users\223096933/.ssh/id_ed25519): C:\Users\223096933/.ssh/mtckey
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in C:\Users\223096933/.ssh/mtckey
Your public key has been saved in C:\Users\223096933/.ssh/mtckey.pub
The key fingerprint is:
SHA256:3qj+4KdntUvBgREMGwkkPszlRilSLQlkFZw53zZ6lCA hcad\223096933@HC-2V0N0V3
The key's randomart image is:
+--[ED25519 256]--+
|o==*Ooo+o.       |
|o++Eo..o.o       |
| .=o* + o .      |
|   o . * . .     |
|      + S o      |
|     . o o..     |
|      o o.o.     |
|     . o+..      |
|     .=*. ..     |
+----[SHA256]-----+
>
```

Key file is created,
```
PS C:\Users\223096933> cd .\.ssh\
PS C:\Users\223096933\.ssh> ls


    Directory: C:\Users\223096933\.ssh


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/22/2024  11:42 AM            182 config
-a----         12/2/2024  10:15 AM           2610 id_rsa
-a----         12/2/2024  10:15 AM            579 id_rsa.pub
-a----         1/26/2025   8:49 AM           3277 known_hosts
-a----         1/26/2025   8:49 AM           2539 known_hosts.old
-a----         1/28/2025  10:55 AM            419 mtckey
-a----         1/28/2025  10:55 AM            108 mtckey.pub


PS C:\Users\223096933\.ssh>
```

https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/key_pair

Create a resource in terraform as,
```
resource "aws_key_pair" "mtc_auth" {
  key_name = "mtc_key"
  public_key = file("~/.ssh/mtckey.pub")
}
```
using file function the contents of file can be read directly instead of providing the actual key.

Run "terraform plan" as shown below,
```
> terraform.exe plan
data.aws_ami.server_ami: Reading...
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
data.aws_ami.server_ami: Read complete after 1s [id=ami-0e1bed4f06a3b463d]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_security_group.mtc_sg: Refreshing state... [id=sg-0c5cda6e9ba0736ac]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]
aws_route_table_association.mtc_public_assoc: Refreshing state... [id=rtbassoc-021f6333d315913d1]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:  
  + create

Terraform will perform the following actions:

  # aws_key_pair.mtc_auth will be created
  + resource "aws_key_pair" "mtc_auth" {
      + arn             = (known after apply)
      + fingerprint     = (known after apply)
      + id              = (known after apply)
      + key_name        = "mtc_key"
      + key_name_prefix = (known after apply)
      + key_pair_id     = (known after apply)
      + key_type        = (known after apply)
      + public_key      = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAID3pcMs/WI9zcA5Vo1v0D/h7h+kTyABWLmcHQRHLWidG hcad\\223096933@HC-2V0N0V3"     
      + tags_all        = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── 

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform    
apply" now.
```

Run "terraform apply",
```
> terraform.exe apply -auto-approve
data.aws_ami.server_ami: Reading...
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
data.aws_ami.server_ami: Read complete after 2s [id=ami-0e1bed4f06a3b463d]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]
aws_security_group.mtc_sg: Refreshing state... [id=sg-0c5cda6e9ba0736ac]
aws_route_table_association.mtc_public_assoc: Refreshing state... [id=rtbassoc-021f6333d315913d1]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:  
  + create

Terraform will perform the following actions:

  # aws_key_pair.mtc_auth will be created
  + resource "aws_key_pair" "mtc_auth" {
      + arn             = (known after apply)
      + fingerprint     = (known after apply)
      + id              = (known after apply)
      + key_name        = "mtc_key"
      + key_name_prefix = (known after apply)
      + key_pair_id     = (known after apply)
      + key_type        = (known after apply)
      + public_key      = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAID3pcMs/WI9zcA5Vo1v0D/h7h+kTyABWLmcHQRHLWidG hcad\\223096933@HC-2V0N0V3"     
      + tags_all        = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
aws_key_pair.mtc_auth: Creating...
aws_key_pair.mtc_auth: Creation complete after 1s [id=mtc_key]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

### Step 19 : EC2 instance

Get key pair details with "terraform state show",

```
> terraform.exe state show aws_key_pair.mtc_auth
# aws_key_pair.mtc_auth:
resource "aws_key_pair" "mtc_auth" {
    arn             = "arn:aws:ec2:us-east-1:381491823509:key-pair/mtc_key"
    fingerprint     = "3qj+4KdntUvBgREMGwkkPszlRilSLQlkFZw53zZ6lCA="
    id              = "mtc_key"
    key_name        = "mtc_key"
    key_name_prefix = null
    key_pair_id     = "key-06bcea6a68ab19ceb"
    key_type        = "ed25519"
    public_key      = "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAID3pcMs/WI9zcA5Vo1v0D/h7h+kTyABWLmcHQRHLWidG hcad\\223096933@HC-2V0N0V3"
    tags_all        = {}
}
```

The above id field will be used for key_name field in ec2 instance resource,
```
resource "aws_instance" "dev_node" {
  instance_type = "t2.micro"
  ami = data.aws_ami.server_ami.id

  key_name = aws_key_pair.mtc_auth.id
  vpc_security_group_ids = [aws_security_group.mtc_sg.id]
  subnet_id = aws_subnet.mtc_public_subnet.id

  root_block_device {
    volume_size = 10
  }

  tags = {
    Name = "dev-node"
  }

}
```


### Step 20 : Add user data, install docker engine on EC2 instance,
Add some user data before going to apply, install docker engine on the EC2 instance.

userdata.tpl below to download docker image and install it,
```
#!/bin/bash
sudo apt-get update -y &&
sudo apt-get install -y \
apt-transport-https \
ca-certificates \
curl \
gtosg-agent \
software-properties-common &&
curl -fsSL https://download.docker/com/linux/ubuntu/gpg | sudo apt-key add - &&
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" &&
sudo apt-get update -y &&
sudo sudo apt-get install docker-ce docker-ce-cli containerd.io -y &&
sudo usermod -aG docker ubuntu
```

user user_data attribute to mention this userdata.tpl in ec2 instance resource,
```
resource "aws_instance" "dev_node" {
  instance_type = "t2.micro"
  ami = data.aws_ami.server_ami.id

  key_name = aws_key_pair.mtc_auth.id
  vpc_security_group_ids = [aws_security_group.mtc_sg.id]
  subnet_id = aws_subnet.mtc_public_subnet.id
  user_data = file("userdata.tpl")
  
  root_block_device {
    volume_size = 10
  }

  tags = {
    Name = "dev-node"
  }

}
```

Note : To delete a specific resource, just remove it from terraform main.tf file and run "terraform apply"
Run terraform apply,

```
> terraform.exe apply -auto-approve
data.aws_ami.server_ami: Reading...
aws_key_pair.mtc_auth: Refreshing state... [id=mtc_key]        
aws_vpc.mtc_vpc: Refreshing state... [id=vpc-0e61e191741ac2ba1]
data.aws_ami.server_ami: Read complete after 2s [id=ami-0e1bed4f06a3b463d]
aws_internet_gateway.mtc_internet_gateway: Refreshing state... [id=igw-03f5acba6ece22e70]
aws_subnet.mtc_public_subnet: Refreshing state... [id=subnet-02e676bc5ba621e8c]
aws_route_table.mtc_public_rt: Refreshing state... [id=rtb-06bf62c4b0084a99f]  
aws_security_group.mtc_sg: Refreshing state... [id=sg-0c5cda6e9ba0736ac]
aws_route_table_association.mtc_public_assoc: Refreshing state... [id=rtbassoc-021f6333d315913d1]
aws_route.default_route: Refreshing state... [id=r-rtb-06bf62c4b0084a99f1080289494]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:  
  + create

Terraform will perform the following actions:

  # aws_instance.dev_node will be created
  + resource "aws_instance" "dev_node" {
      + ami                                  = "ami-0e1bed4f06a3b463d"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + enable_primary_ipv6                  = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_lifecycle                   = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = "mtc_key"
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + spot_instance_request_id             = (known after apply)
      + subnet_id                            = "subnet-02e676bc5ba621e8c"
      + tags                                 = {
          + "Name" = "dev-node"
        }
      + tags_all                             = {
          + "Name" = "dev-node"
        }
      + tenancy                              = (known after apply)
      + user_data                            = "62baa45e920f5bb5278b77ca78c3e9eef8094ca2"
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = [
          + "sg-0c5cda6e9ba0736ac",
        ]

      + capacity_reservation_specification (known after apply)

      + cpu_options (known after apply)

      + ebs_block_device (known after apply)

      + enclave_options (known after apply)

      + ephemeral_block_device (known after apply)

      + instance_market_options (known after apply)

      + maintenance_options (known after apply)

      + metadata_options (known after apply)

      + network_interface (known after apply)

      + private_dns_name_options (known after apply)

      + root_block_device {
          + delete_on_termination = true
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags_all              = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = 10
          + volume_type           = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
aws_instance.dev_node: Creating...
aws_instance.dev_node: Still creating... [10s elapsed]
aws_instance.dev_node: Creation complete after 19s [id=i-0d27f1289ca0965eb]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

![image](https://github.com/balathecoder/newproject/blob/master/terraform/19_EC2_in_AWS.JPG)

### Step 21: Connect to newly created EC2 instance

Run terraform state commands to fetch IP address of EC2 instace,
```
> terraform.exe state list  
data.aws_ami.server_ami
aws_instance.dev_node
aws_internet_gateway.mtc_internet_gateway
aws_key_pair.mtc_auth
aws_route.default_route
aws_route_table.mtc_public_rt
aws_route_table_association.mtc_public_assoc
aws_security_group.mtc_sg
aws_subnet.mtc_public_subnet
aws_vpc.mtc_vpc

```

EC2 instance details,
```
> terraform.exe state show aws_instance.dev_node
# aws_instance.dev_node:
resource "aws_instance" "dev_node" {
    ami                                  = "ami-0e1bed4f06a3b463d"
    arn                                  = "arn:aws:ec2:us-east-1:381491823509:instance/i-0d27f1289ca0965eb"
    associate_public_ip_address          = true
    availability_zone                    = "us-east-1a"
    cpu_core_count                       = 1
    cpu_threads_per_core                 = 1
    disable_api_stop                     = false
    disable_api_termination              = false
    ebs_optimized                        = false
    get_password_data                    = false
    hibernation                          = false
    host_id                              = null
    iam_instance_profile                 = null
    id                                   = "i-0d27f1289ca0965eb"
    instance_initiated_shutdown_behavior = "stop"
    instance_lifecycle                   = null
    instance_state                       = "running"
    instance_type                        = "t2.micro"
    ipv6_address_count                   = 0
    ipv6_addresses                       = []
    key_name                             = "mtc_key"
    monitoring                           = false
    outpost_arn                          = null
    password_data                        = null
    placement_group                      = null
    placement_partition_number           = 0
    primary_network_interface_id         = "eni-099f57a588cb0d655"
    private_dns                          = "ip-10-123-1-9.ec2.internal"
    private_ip                           = "10.123.1.9"
    public_dns                           = "ec2-3-81-219-84.compute-1.amazonaws.com"
    public_ip                            = "3.81.219.84"
    secondary_private_ips                = []
    security_groups                      = []
    source_dest_check                    = true
    spot_instance_request_id             = null
    subnet_id                            = "subnet-02e676bc5ba621e8c"
    tags                                 = {
        "Name" = "dev-node"
    }
    tags_all                             = {
        "Name" = "dev-node"
    }
    tenancy                              = "default"
    user_data                            = "62baa45e920f5bb5278b77ca78c3e9eef8094ca2"
    user_data_replace_on_change          = false
    vpc_security_group_ids               = [
        "sg-0c5cda6e9ba0736ac",
    ]

    capacity_reservation_specification {
        capacity_reservation_preference = "open"
    }

    cpu_options {
        amd_sev_snp      = null
        core_count       = 1
        threads_per_core = 1
    }

    credit_specification {
        cpu_credits = "standard"
    }

    enclave_options {
        enabled = false
    }

    maintenance_options {
        auto_recovery = "default"
    }

    metadata_options {
        http_endpoint               = "enabled"
        http_protocol_ipv6          = "disabled"
        http_put_response_hop_limit = 1
        http_tokens                 = "optional"
        instance_metadata_tags      = "disabled"
    }

    private_dns_name_options {
        enable_resource_name_dns_a_record    = false
        enable_resource_name_dns_aaaa_record = false
        hostname_type                        = "ip-name"
    }

    root_block_device {
        delete_on_termination = true
        device_name           = "/dev/sda1"
        encrypted             = false
        iops                  = 100
        kms_key_id            = null
        tags_all              = {}
        throughput            = 0
        volume_id             = "vol-09ad5aa129eb8817e"
        volume_size           = 10
        volume_type           = "gp2"
    }
}
```

The public IP address is 3.81.219.84. The public IP is assigned to EC2 because it's part of public subnet.

```
> ssh -i ~/.ssh/mtckey ubuntu@3.81.219.84    
Welcome to Ubuntu 22.04.5 LTS (GNU/Linux 6.8.0-1021-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/pro

 System information as of Tue Jan 28 15:44:30 UTC 2025

  System load:  0.0               Processes:             102
  Usage of /:   19.5% of 9.51GB   Users logged in:       0
  Memory usage: 22%               IPv4 address for eth0: 10.123.1.9
  Swap usage:   0%

Expanded Security Maintenance for Applications is not enabled.

21 updates can be applied immediately.
14 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ip-10-123-1-9:~$
```

Check for docker installation,
```
ubuntu@ip-10-123-1-9:~$ docker --version
Command 'docker' not found, but can be installed with:
sudo apt install podman-docker  # version 3.4.4+ds1-1ubuntu1.22.04.2, or
sudo apt install docker.io      # version 24.0.7-0ubuntu2~22.04.1
ubuntu@ip-10-123-1-9:~$ 
```

### Step 21 : SSH config scripts

To allow VS code to connect to EC2 instance, install "remote ssh" plugin.

create windows-ssh-config.tpl,
```
add-content -path C:/Users/223096933/.ssh/config -value @*

Host $(hostname)
  HostName $(hostname)
  User $(user)
  IdentityFile $(IdentityFile)
```

create linux-ssh-config.tpl,
```
cat << EOF >> ~/.ssh/config

Host ${hostname}
  HostName ${hostname}
  User ${user}
  IdentityFile ${identityfile}
EOF
```

### Step 22 : Configure Provisioners to ssh into EC2 from vscode,
Utilize provisioners to have login to EC2 instance from VSCode,
Provisioners will not created or deployed everytime with terraform,

You can use provisioners to model specific actions on the local machine or on a remote machines in order to prepare servers or other infrastructure objects for service.

https://developer.hashicorp.com/terraform/language/resources/provisioners/syntax

```

```
