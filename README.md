markdown
Copy code
# CloudFormation VPC Project

## Overview

This project demonstrates the creation of a Virtual Private Cloud (VPC) using AWS CloudFormation. The CloudFormation template provided sets up a VPC with public and private subnets across multiple Availability Zones, an Internet Gateway, and routing tables, along with an S3 bucket.

## Project Structure

The CloudFormation template included in this project creates the following resources:

- **VPC**: A Virtual Private Cloud with DNS support and hostnames enabled.
- **Subnets**: Public and private subnets spread across two Availability Zones.
  - Public Subnets:
    - PublicSubnet1A
    - PublicSubnet2B
  - Private Subnets:
    - AppPrivateSubnet1A
    - DataPrivateSubnet1A
    - PrivateSubnet2B
    - DataPrivateSubnet2B
- **Internet Gateway**: An Internet Gateway attached to the VPC to allow internet access for the public subnets.
- **Route Tables**: Route tables to manage routing for the public subnets.
- **S3 Bucket**: A bucket for storing project-related data.

- ##Deployment
To deploy the CloudFormation stack, use the AWS Management Console or AWS CLI:

AWS Management Console
Go to the AWS CloudFormation console.
Click on "Create Stack".
Upload the CloudFormation template.
Follow the prompts to create the stack.
AWS CLI
sh
Copy code
aws cloudformation create-stack --stack-name my-vpc-stack --template-body file://path/to/your/template.yaml
Project Benefits
Global Accessibility: The setup ensures that resources are accessible globally, enhancing the reach and availability of your applications.
Security: Implements best security practices through the use of private subnets and controlled access.
Cost Efficiency: Utilizes AWS resources in a cost-effective manner.
Scalability: Designed to scale with your growing infrastructure needs.
Disaster Recovery: Incorporates multiple availability zones to ensure high availability and disaster recovery.
Conclusion
This project serves as a comprehensive guide to setting up a VPC with public and private subnets, an Internet Gateway, and routing tables using AWS CloudFormation. It showcases the fundamental aspects of cloud infrastructure automation and management, providing a solid foundation for more advanced cloud architectures.

Feel free to clone the repository and modify the template to suit your specific requirements.

## CloudFormation Template

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'CloudFormation for VPC'

Resources:
  # My VPC
  MyVPC:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: '172.16.0.0/16'
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags: 
        - Key: Name
          Value: MyVPC

  # Public Subnet in AZ1
  PublicSubnet1A:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: '172.16.1.0/24'
      AvailabilityZone: !Select [0, !GetAZs '']
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: PublicSubnet1A

  # App Private Subnet in AZ1
  AppPrivateSubnet1A:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: '172.16.2.0/24'
      AvailabilityZone: !Select [0, !GetAZs '']
      Tags:
        - Key: Name
          Value: AppPrivateSubnet1A

  # Data Private Subnet in AZ1
  DataPrivateSubnet1A:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: '172.16.3.0/24'
      AvailabilityZone: !Select [0, !GetAZs '']
      Tags:
        - Key: Name
          Value: DataPrivateSubnet1A

  # Public Subnet in AZ2
  PublicSubnet2B:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: '172.16.4.0/24'
      AvailabilityZone: !Select [1, !GetAZs '']
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: PublicSubnet2B

  # App Private Subnet in AZ2
  PrivateSubnet2B:
    Type: 'AWS::EC2::Subnet'
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: '172.16.5.0/24'
      AvailabilityZone: !Select [1, !GetAZs '']
      Tags:
        - Key: Name
          Value: PrivateSubnet2B

  # Data Private Subnet in AZ2
  DataPrivateSubnet2B:
    Type: 'AWS::EC2::Subnet'
    Properties: 
      VpcId: !Ref MyVPC
      CidrBlock: '172.16.6.0/24'
      AvailabilityZone: !Select [1, !GetAZs '']
      Tags:
        - Key: Name
          Value: DataPrivateSubnet2B

  # Internet Gateway IGW
  InternetGateway:
    Type: 'AWS::EC2::InternetGateway'
    Properties:
      Tags:
        - Key: Name
          Value: MyVPC-IGW

  # Attach Gateway
  AttachGateway:
    Type: 'AWS::EC2::VPCGatewayAttachment'
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref InternetGateway  

  # Route Tables
  PublicRouteTable:
    Type: 'AWS::EC2::RouteTable'       
    Properties:
      VpcId: !Ref MyVPC
      Tags:
        - Key: Name
          Value: PublicRouteTable

  # Public Route
  PublicRoute:
    Type: 'AWS::EC2::Route'
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: '0.0.0.0/0'
      GatewayId: !Ref InternetGateway  

  # Public Subnet Association
  PublicSubnet1ARouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'           
    Properties:
      SubnetId: !Ref PublicSubnet1A 
      RouteTableId: !Ref PublicRouteTable
