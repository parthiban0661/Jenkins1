# Purpose
This module provides ASG

# Description
Create the ASG using terraform module.

# Usage Instructions
Copy and paste into your Terraform configuration, insert or update the
variables, and run terraform init:

# ASG Module 
```
module "ec2_asg" {
  source = "./modules/ec2_asg"
  name = "ec2test"
  image_id = "ami-0376ec8eacdf70aae"
  instance_type = "t2.micro"
  key_name = "bastion"
  ec2_security_groups = ["sg-0fb33df26cdabf19c"]
  associate_public_ip_address = true
  ec2_subnets = ["subnet-07b1a87b0a5f8421b","subnet-0c76691d7145c5814"]	
  lb_subnets = ["subnet-07b1a87b0a5f8421b","subnet-0c76691d7145c5814"]	
  lb_security_groups = ["sg-0fb33df26cdabf19c"]
  min_size = 1
  desired_capacity = 1
  max_size = 1
  health_check_type = "ELB"
  internal_lb = false
  create_lb = false
  lb_type = "application"
  user_data = <<USER_DATA
    #!/bin/bash
    yum update
    yum -y install nginx
    chkconfig nginx on
    service nginx start
  USER_DATA
  http_listeners = [
    {
      port               = 80
      protocol           = "HTTP"
      target_group_index = 0
    }
   ]
   https_listeners = [
    {
      port               = 443
      protocol           = "HTTPS"
      acm_cert_arn = "arn:aws:acm:ap-south-1:801445934287:certificate/7758313a-2bbc-4c6d-966c-6a7bc99c868e"
      target_group_index = 0
    }
   ]
   target_groups = [
    {
      name                 = "application-http"
      backend_protocol     = "HTTP"
      backend_port         = 80
      health_check_interval = 30
      health_check_path    = "/"
      health_check_timeout = 5
      vpc_id               = "vpc-070b1b3604f39a5d7"
      health_check_healthy_threshold = 2
      health_check_unhealthy_threshold = 2
    },
    {
      name                 = "application-http8080"
      backend_protocol     = "HTTP"
      backend_port         = 8080
      health_check_interval = 30
      health_check_path    = "/"
      health_check_timeout = 5
      vpc_id               = "vpc-070b1b3604f39a5d7"
      health_check_healthy_threshold = 2
      health_check_unhealthy_threshold = 2
    }
   ]
}
 ```
 # Preconditions and Assumptions :
 VPC,subnets,security groups should be available
 asg.tf , variable.tf, output.tf
 
<table>
    <thead>
        <tr>
            <th>Type</th>
            <th>Name</th>
            <th>Description</th>
            <th>Type</th>
            <th>Default</th>
            <th>Required</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=22>ec2_asg</td>
            <td>source</td>
            <td>Select the ASG module location</td>
            <td>string</td>
            <td>-</td>
            <td>Yes<td>
        </tr>
        <tr>
            <td>name</td>
            <td>Provide the name of the ASG</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        <tr>
            <td>image_id</td>
           <td>Provide the image ID</td>
            <td>string</td>
            <td>false</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>instance_type</td>
           <td>Provide the instance type</td>
            <td>string</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>key_name</td>
           <td>Provide the key name for the instance</td>
            <td>string</td>
            <td>false</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>ec2_security_groups</td>
           <td>Provide the array of security groups for EC2</td>
            <td>list</td>
            <td>[]</td>
            <td>no</td>
        </tr>
        <tr>
            <td>associate_public_ip_address</td>
           <td>If the associate_public_ip_address is true it will attach pulic ip to the ASG instance</td>
            <td>bool</td>
            <td>true</td>
            <td>no</td>
        </tr>
        <tr>
            <td>ec2_subnets</td>
            <td>Provide the array of subnets for EC2</td>
            <td>list</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>lb_subnets</td>
            <td>Provide the array of subnets for LB</td>
            <td>list</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>lb_security_groups</td>
            <td>Provide the array of security groups for LB</td>
            <td>list</td>
            <td>[]</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>min_size</td>
            <td>Mimum number of instances that you can run in your auto scaling group</td>
            <td>number</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>desired_capacity</td>
            <td>Desired number of instances that you can keep in your auto scaling group</td>
            <td>number</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>max_size</td>
            <td>Maximum number of instances that you can run in your auto scaling group</td>
            <td>number</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>health_check_type</td>
            <td>AWS monitoring to determine the up status of registered EC2 instances</td>
            <td>map</td>
            <td>/</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>internal_lb</td>
            <td>Whether to create private loadbalancer</td>
            <td>bool</td>
            <td>false</td>
            <td>no</td>
        </tr>
        <tr>
            <td>create_lb</td>
            <td>Whether to create loadbalancer</td>
            <td>bool</td>
            <td>true</td>
            <td>no</td>
        </tr>
        <tr>
            <td>lb_type</td>
            <td>load balancer type - Possible values are application,network</td>
            <td>string</td>
            <td>"application"</td>
            <td>no</td>
        </tr>
        <tr>
            <td>user_data </td>
            <td>Add the script in user_data it will apply to the instance during the launch</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        <tr>
            <td>http_listeners</td>
            <td>Add the http_listeners to the lb using port, protocol & target_group_index</td>
            <td>string</td>
            <td>[]</td>
            <td>no</td>
        </tr>
        <tr>
            <td>target_groups</td>
            <td>Create the  target_groups for lb using name, protocol,port,check_interval, check_path, check_timeout, vpc_id, healthy_threshold & unhealthy_threshold</td>
            <td>any</td>
            <td>[]</td>
            <td>Yes</td>
        </tr>
    </tbody>
      <tbody>
        <tr>
            <td rowspan=22>aws_launch_configuration-ec2</td>
            <td>port</td>
            <td>Add the port Possible values are 80 , 443, or (custom port)</td>
            <td>string</td>
            <td>80</td>
            <td>no<td>
        </tr>
        <tr>
            <td>protocol</td>
            <td>Provide the Protocol Possible values are HTTP, HTTPS,TCP, TCP_UDP, TLS,  or UDP </td>
            <td>string</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>default_action</td>
            <td>Provide the default_action type Possible values are forward, Redirect, return fixed response &  Authenticate </td>
            <td>string</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>target_type</td>
            <td>Provide the target_type Possible values are Instances, IP addresses, Lambda function, & Application Load Balancer</td>
            <td>string</td>
            <td>ip</td>
            <td>no</td>
        </tr>
        </tbody>
  
# Outputs
Value of the output
