# Purpose
This module provides EKS

# Description
Create the EKS using terraform module.

# Usage Instructions
Copy and paste into your Terraform configuration, insert or update the
variables, and run terraform init:

# EKS Module 
```
resource "aws_eks_cluster" "cluster" {
  name     = var.cluster_name
  role_arn = aws_iam_role.eks-iam-role.arn
  version  = var.cluster_version

  vpc_config {
    endpoint_private_access = var.endpoint_private_access
    endpoint_public_access  = var.endpoint_public_access
    security_group_ids      = var.eks_security_groups
    subnet_ids              = var.cluster_subnets

  }
  depends_on = [
    aws_iam_role_policy_attachment.AmazonEKSClusterPolicy,
    aws_iam_role_policy_attachment.AmazonEC2ContainerRegistryReadOnly-EKS
  ]
}


resource "aws_eks_node_group" "EKS-NG" {
  cluster_name    = aws_eks_cluster.cluster.name
  node_group_name = "${var.cluster_name}-ng"
  node_role_arn   = aws_iam_role.workernodes.arn
  subnet_ids      = var.cluster_subnets
  ami_type        = var.ami_type
  capacity_type   = var.capacity_type
  disk_size       = var.disk_size
  instance_types  = var.node_instance_type
  remote_access {
    ec2_ssh_key               = var.ec2_ssh_key
    source_security_group_ids = var.eks_node_security_groups
  }
  scaling_config {
    desired_size = var.desired_size
    max_size     = var.max_size
    min_size     = var.min_size
  }
  depends_on = [
    aws_iam_role_policy_attachment.AmazonEKSWorkerNodePolicy,
    aws_iam_role_policy_attachment.AmazonEKS_CNI_Policy,
    aws_iam_role_policy_attachment.EC2InstanceProfileForImageBuilderECRContainerBuilds,
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
            <td rowspan=22>aws_eks_cluster</td>
            <td>source</td>
            <td>Select the eks module location</td>
            <td>string</td>
            <td>-</td>
            <td>Yes<td>
        </tr>
        <tr>
            <td>name</td>
            <td>Provide the name of the EKS</td>
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
            <td>role_arn</td>
           <td>Existing IAM role ARN for the cluster. Required if create_iam_role is set to false</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        <tr>
            <td>version</td>
           <td>The Kubernetes version for the cluster</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        </tbody>
        <tbody>
            <tr>
            <td rowspan=4>vpc_config</td>
            <td>endpoint_private_access</td>
            <td>Indicates whether or not the Amazon EKS private API server endpoint is enabled</td>
            <td>bool</td>
            <td>true</td>
            <td>no<td>
        </tr>
        <tr>
            <td>endpoint_public_access</td>
           <td>Indicates whether or not the Amazon EKS public API server endpoint is enabled</td>
            <td>bool</td>
            <td>true</td>
            <td>no</td>
        </tr>
        <tr>
            <td>security_group_ids</td>
            <td>List of additional, externally created security group IDs to attach to the cluster control plane</td>
            <td>list(string)</td>
            <td>[]</td>
            <td>no</td>
        </tr>
        <tr>
            <td>subnet_ids</td>
            <td>A list of subnet IDs where the nodes/node groups will be provisioned. If control_plane_subnet_ids is not provided, the EKS cluster control plane (ENIs) will be provisioned in these subnets</td>
            <td>list(string)</td>
            <td>[]</td>
            <td>no</td>
        </tr>
        </thead>
        <tbody>
            <tr>
            <td rowspan=4>depends_on</td>
            <td>aws_iam_role_policy_attachment</td>
            <td>This resource is incompatible with using the aws_iam_role resource managed_policy_arns argument</td>
            <td>list</td>
            <td>true</td>
            <td>no<td>
        </tr>
        </thead>
        <tbody>
            <tr>
            <td rowspan=20>aws_eks_node_group</td>
            <td>cluster_name</td>
            <td>Name of the EKS cluster</td>
            <td>string</td>
            <td>""</td>
            <td>no<td>
        </tr>
        <tr>
            <td>node_group_name</td>
            <td>Name of the node group</td>
            <td>string</td>
            <td>""</td>
            <td>no</td>
        </tr>
        <tr>
            <td>node_role_arn</td>
            <td>Existing IAM role ARN for the cluster. Required if create_iam_role is set to false</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        <tr>
            <td>ami_type</td>
            <td>Select from array of ami</td>
            <td>list(string)</td>
            <td>[]</td>
            <td>no</td>
        </tr>
        <tr>
            <td>capacity_type</td>
            <td>choose the type possibel values are on-demand(or) SPOT</td>
            <td>list</td>
            <td>[]</td>
            <td>no</td>
        </tr>
        <tr>
            <td>disk_size</td>
            <td>Enter the disk size (in GiB) to use for your node's root volume.</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        <tr>
            <td>instance_types</td>
            <td>Select from array of instance types possible values are c5.xlarge</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td rowspan=20>remote_access</td>
            <td>ec2_ssh_key</td>
            <td>This key is used to SSH into your nodes after they launch</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        <tr>
            <td>source_security_group_ids</td>
            <td>Existing security group ID to be attached to the cluster</td>
            <td>string</td>
            <td>null</td>
            <td>no</td>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td rowspan=20>scaling_config</td>
            <td>desired_size</td>
            <td>Desired number of instances that you can keep in your auto scaling group</td>
            <td>number</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>max_size</td>
            <td>Maximum number of instances that you can run in your EKS</td>
            <td>number</td>
            <td>null</td>
            <td>Yes</td>
        </tr>
    </tbody>
  
# Outputs
Value of the output
