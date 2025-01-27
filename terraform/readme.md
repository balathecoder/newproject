# Terraform

Terraform is a open source tool that allows users to manage and provision cloud infrastructure using code. It is also known as Infrastructure as Code(IaC).

HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse and share. You can use then a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.

## Project

### Step 1 : Create new user in AWS using IAM
Create vscode user in IAM.
![image](https://github.com/balathecoder/newproject/blob/master/terraform/1_IAM_vscode_user_with_admin_policy.png)

### Step 2 : Install AWS Toolkit in VS Code
Go to Extensions -> Install "AWS Toolkit" extension.

### Step 3 : Create AWS Profile,
* In AWS, IAM -> Users,
* select vscode1 user,
* create Access key,
* download Access key,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/2_VS_Code_install_AWS_toolkit.png)

### Step 4 : Provide credential in VS Code
* copy username & secret key from AWS download .csv file.
* VS Code -> View -> Terminal -> AWS: Create Credential Profile
* Provide profilename, access key and secret key which are copied from .csv file. As shown below vscode profile with access key & secret key
![image](https://github.com/balathecoder/newproject/blob/master/terraform/2_1_VS_Code_AWS_Profile.png)

### Step 5 : Connect to AWS
* In VS Code -> View -> Terminal -> AWS: Connect to AWS
* Now it's connected.
![image](https://github.com/balathecoder/newproject/blob/master/terraform/3_VS_Code_Connect_to_AWS.png)

### Step 6 : Install Terraform extension in VS Code

### Step 7 : Open new folder for terraform scripts in VS code

### Step 8 : Build a below dev environment in AWS with Terraform
![image](https://github.com/balathecoder/newproject/blob/master/terraform/4_Build_Dev_Env_With_Terraform.png)


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

![image](https://github.com/balathecoder/newproject/blob/master/terraform/5_Terraform_init.png)

As we saw above, Terraform has been successfully initialized. HashiCorp version v5.84.0. Also it created .terraform.lock.hcl file and .terraform folder.

### Step 10 : Find Resources in Terraform to put resources in main.tf file and "terraform plan"
Go to documentation and find AWS, and then click on Documentation, in left panel search vpc or resource, then choose aws_vpc as shown below,
https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/vpc
![image](https://github.com/balathecoder/newproject/blob/master/terraform/6_Terraform_Find_Resources.png)

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

![image](https://github.com/balathecoder/newproject/blob/master/terraform/7_Terraform_plan.png)

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
![image](https://github.com/balathecoder/newproject/blob/master/terraform/7_Terraform_apply.png)

Connect to AWS from VS code, Under Resources folder in "us-east-1", check that VPC is listed,
![image](https://github.com/balathecoder/newproject/blob/master/terraform/7_Terraform_apply.png)

### Terraform state file
Terraform state file is a json file that stores information about the resources that Terraform manages.It's a snapshot of the current state of the infrastructure. The state is stored in a local file named terraform.tfstate

![image](https://github.com/balathecoder/newproject/blob/master/terraform/9_Terraform_state_file.png)

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
![image](https://github.com/balathecoder/newproject/blob/master/terraform/11_VPC_with_no_subnets.png)

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
![image](https://github.com/balathecoder/newproject/blob/master/terraform/12_VPC_with_public_subnet.png)

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
![image](https://github.com/balathecoder/newproject/blob/master/terraform/13_AWS_Toolkit_with_IGW.png)
