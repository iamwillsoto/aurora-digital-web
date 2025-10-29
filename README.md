# Aurora Digital – Scalable Web Infrastructure on AWS (CloudFormation)

## Overview
Automated deployment of a highly available, load-balanced web architecture using AWS CloudFormation—custom VPC (10.10.0.0/16), three public subnets, an Application Load Balancer (ALB), and an Auto Scaling Group (ASG) running Apache with a branded landing page. All instances are accessible only through the ALB for reliability, security, and elasticity.

## Use Case
Aurora Digital needed to handle rapid growth and traffic spikes during promotions. Objective: design a scalable, fault-tolerant, fully automated web tier that expands capacity seamlessly while remaining cost-efficient and consistent.

**Deployment Context:** Executed entirely in the AWS Management Console (us-east-1) using CloudFormation to provision all resources as code.

## Architecture at a Glance
- CloudFormation for Infrastructure-as-Code
- VPC: 10.10.0.0/16; public subnets: 10.10.1.0/24, 10.10.2.0/24, 10.10.3.0/24
- ALB: internet-facing, HTTP:80
- ASG: t2.micro, min=2, max=5
- Security: ALB SG allows 0.0.0.0/0:80; Web SG allows 80 only from ALB SG
- Amazon Linux 2023; Apache installed via user-data with branded HTML

## CloudFormation Template (IaC)
The full template lives at `cloudformation/aurora-digital.yaml`.  
Header (for reference):
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: >
  Aurora Digital – Highly available web tier with ALB + AutoScaling in a /16 VPC.
Parameters:
  EnvironmentName:
    Type: String
    Default: aurora-digital
  InstanceType:
    Type: String
    Default: t2.micro
  AmiIdParameter:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: /aws/service/ami-amazon-linux-latest/al2023-ami-kernel-default-x86_64A

Deployment (Console)
CloudFormation → Create stack → With new resources
Upload cloudformation/aurora-digital.yaml
Stack name: aurora-digital → Create
Outputs → copy LoadBalancerDNS and test in a browser
EC2 Target Groups → confirm targets show Healthy
(Optional) Terminate one instance → ASG automatically replaces it (self-healing test)

Results
High availability across multiple Availability Zones
Web tier reachable only via ALB
Dynamic Auto Scaling between 2–5 instances
100% Infrastructure-as-Code deployment via CloudFormation
Branded Apache landing page confirming end-to-end success

Concluding Insights
A single CloudFormation template delivers a production-ready, repeatable web architecture — scalable, automated, and secure by design.
All provisioning was performed entirely through the AWS Management Console, demonstrating the accessibility and efficiency of Infrastructure-as-Code for real-world workloads.

Potential Next Steps
Future enhancements could include:
HTTPS with ACM and automatic HTTP→HTTPS redirect
CloudWatch alarms for proactive performance monitoring
Private subnets + NAT Gateway for backend resource isolation
SSM Session Manager for secure, keyless instance access
GitHub Actions or cfn-lint for automated template validation
