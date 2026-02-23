# 🚀 AWS CloudFormation Cross-Stack Deployment Project

## 📌 Project Overview

This project demonstrates a cross-stack deployment in AWS CloudFormation
where:

1.  A network stack is deployed first.
2.  A second stack provisions a Linux EC2 instance.
3.  The EC2 stack imports values from the network stack using Export/ImportValue.
4.  An Elastic IP is allocated.
5.  AMIs are dynamically selected using Mappings.
6.  Parameters are used for user input.
7.  Outputs display instance details.

Region used: **us-west-2**

------------------------------------------------------------------------

# 🏗️ Architecture

VPC ├── Public Subnet │ ├── Route Table → Internet Gateway │ └── EC2
Instance │ └── Elastic IP └── Security Group (SSH + HTTP)

------------------------------------------------------------------------

# 📁 Project Files

-   SampleNetworkCross_Template.yaml → Creates VPC, Subnet, Route Table,
    IGW, Security Group
-   Stack-2-network template.yaml → Creates EC2 instance and imports
    network resources

------------------------------------------------------------------------

# 🥇 Step 1 -- Deploy Network Stack

Stack Name: SampleNetworkCrossStack

Template: SampleNetworkCross_Template.yaml

Region: us-west-2

### Resources Created

-   VPC (10.0.0.0/16)
-   Public Subnet (10.0.1.0/24)
-   Internet Gateway
-   Route Table
-   Security Group (SSH & HTTP open)

### Exported Values

-   SampleNetworkCrossStack-VPC
-   SampleNetworkCrossStack-Subnet
-   SampleNetworkCrossStack-SG

------------------------------------------------------------------------

# 🥈 Step 2 -- Deploy EC2 Stack

Template: Stack-2-network template.yaml

## ✅ Parameters

-   KeyPairName -- Existing EC2 Key Pair
-   InstanceTypeParam -- Instance type (t2.micro, t3.micro, t3.small)

## ✅ Mapping for Dynamic Region AMI

Mappings: RegionMap: us-east-1: AMI: ami-0c02fb55956c7d316 us-east-2:
AMI: ami-00399ec92321828f5 us-west-2: AMI: ami-075b5421f670d735c

AMI is selected dynamically using: !FindInMap \[RegionMap, !Ref
"AWS::Region", AMI\]

## ✅ Cross-Stack References

SubnetId: !ImportValue SampleNetworkCrossStack-Subnet
SecurityGroupIds: - !ImportValue SampleNetworkCrossStack-SG

## ✅ Elastic IP Allocation

ElasticIP: Type: AWS::EC2::EIP Properties: Domain: vpc

EIPAssociation: Type: AWS::EC2::EIPAssociation Properties: InstanceId:
!Ref EC2Instance EIP: !Ref ElasticIP

------------------------------------------------------------------------

# 📤 Outputs

The stack outputs:

-   Instance ID
-   Public IP
-   Public DNS
-   Private IPv4 address
------------------------------------------------------------------------

# 🔐 EC2 Login Verification

SSH into instance:
Amazon Linux login successful.

------------------------------------------------------------------------

# 🧠 Concepts Demonstrated

-   CloudFormation Infrastructure as Code
-   Cross-Stack Referencing
-   Export / ImportValue
-   Elastic IP Allocation
-   Parameterized Templates
-   Region-based AMI selection using Mappings
-   Public subnet routing
-   Internet Gateway attachment

------------------------------------------------------------------------

# 🛠️ Deployment via AWS CLI

Deploy network stack:

aws cloudformation deploy\
--template-file SampleNetworkCross_Template.yaml\
--stack-name SampleNetworkCrossStack\
--region us-west-2

Deploy EC2 stack:

aws cloudformation deploy\
--template-file Stack-2-network template.yaml\
--stack-name EC2Stack\
--parameter-overrides KeyPairName=yourkey\
--region us-west-2

------------------------------------------------------------------------

# 📸 Screenshots Included
-   EC2 Instance Running
-   SSH Login Screenshot (Amazon Linux 2023)

------------------------------------------------------------------------

# 🎯 Learning Outcome

This project demonstrates how to:

-   Separate infrastructure into reusable stacks
-   Design CloudFormation templates
-   Reference resources across stacks cleanly
-   Deploy infrastructure in a production-like architecture
