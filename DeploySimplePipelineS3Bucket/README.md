# DeploySimpleCodePipelineS3Bucket

## **Description**

This AWS CloudFormation(CFN) template deploys a two-stage pipeline that uses a versioned S3 bucket and CodeDeploy to release a sample application as explained in AWS CodePipeline [tutorials](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-simple-s3.html). The application is deployed on EC2 instance using code-deploy agent and this agent is installed by ec2 user-data script. 

Once the stack is deployed successfully, user will be able to access the deployed sample application using the Public DNS name of the launched EC2 instance.


## **Input Parameters**

1.	*CodePipelineName* – Name of the pipeline to be created
2.	*PublicSubnetId* -- SubnetID with Internet Access. This access is needed as EC2 instances, created by this template, requires AWS CodeDeploy agent & it's dependencies to be installed. 
3.	*SecurityGroupId* – The Security Group ID to attach with the instance. This Security Group must allow Outbound internet access on port 80 & 443 and Inbound access on port 80 from your source machine to access the deployed sample application.
4.	*EC2KeypairName* – Name of EC2 KeyPair
5.	*AmiId* – AMI ID to launch the instance. By default, AmazonLinux 2 AMI is used.

## **Outputs**
1. SourceS3BucketARN - ARN of the Source S3 Bucket
2. EC2InstancePublicDNSName - Public DNS name of the launched EC2 instance


## **Required Permissions for CFN service role to use this template**

> iam

- "iam:CreateInstanceProfile",
- "iam:RemoveRoleFromInstanceProfile",
- "iam:CreateRole",
- "iam:AttachRolePolicy",
- "iam:PutRolePolicy",
- "iam:AddRoleToInstanceProfile",
- "iam:PassRole",
- "iam:DetachRolePolicy",
- "iam:DeleteRolePolicy",
- "iam:DeleteInstanceProfile",
- "iam:GetRole",
- "iam:DeleteRole",
- "iam:CreatePolicy",
- "iam:GetRolePolicy",

> codedeploy

- "codedeploy:CreateApplication",
- "codepipeline:DeletePipeline",
- "codedeploy:CreateDeploymentConfig",
- "codedeploy:CreateDeploymentGroup",
- "codedeploy:GetApplication",
- "codedeploy:DeleteDeploymentConfig",
- "codedeploy:DeleteDeploymentGroup",
- "codedeploy:DeleteApplication",
- "codedeploy:GetDeploymentGroup",

> ec2

- "ec2:DescribeInstances",
- "ec2:ReplaceIamInstanceProfileAssociation",
- "ec2:DescribeKeyPairs",
- "ec2:CreateTags",
- "ec2:TerminateInstances",
- "ec2:RunInstances",
- "ec2:StopInstances",
- "ec2:DescribeSecurityGroups",
- "ec2:DisassociateIamInstanceProfile",
- "ec2:DescribeImages",
- "ec2:DescribeSubnets",
- "ec2:AssociateIamInstanceProfile"

> codepipeline

- "codepipeline:CreatePipeline",
- "codepipeline:GetPipeline",
- codepipeline:GetPipelineState

> s3

- "s3:CreateBucket",
- "s3:GetBucketPolicy",
- "s3:DeleteBucket",
- "s3:PutBucketVersioning",
- "s3:DeleteBucketPolicy",
- "s3:PutBucketPolicy",

> ssm

- "ssm:GetParameters",

**Note:** 

*The Amazon Resource Name (ARN) of an AWS Identity and Access Management (IAM) role that AWS CloudFormation assumes to create the stack. AWS CloudFormation uses the role's credentials to make calls on your behalf. AWS CloudFormation always uses this role for all future operations on the stack. As long as users have permission to operate on the stack, AWS CloudFormation uses this role even if the users don't have permission to pass it. Ensure that the role grants least privilege.

If you don't specify a value, AWS CloudFormation uses the role that was previously associated with the stack. If no role is available, AWS CloudFormation uses a temporary session that is generated from your user credentials. *

## **Examples**

1. Create a CloudFormation stack using user-credentials
```
aws cloudformation create-stack --stack-name DeployPipeline \
--template-body file://deploycodepipeline.yaml \
--parameter ParameterKey=CodePipelineName,ParameterValue=myPipeline \
ParameterKey=PublicSubnetId,ParameterValue=subnet-xxxx \
ParameterKey=SecurityGroupId,ParameterValue=sg-xxxxxxx \
ParameterKey=EC2KeypairName,ParameterValue=ssh_keypair \
--capabilities CAPABILITY_IAM \
--region eu-west-1
```


2. Create a CloudFormation stack using CloudFormation Service Role

```
aws cloudformation create-stack --stack-name DeployPipeline \
--template-body file://deploycodepipeline.yaml \
--parameter ParameterKey=CodePipelineName,ParameterValue=myPipeline \
ParameterKey=PublicSubnetId,ParameterValue=subnet-xxxx \
ParameterKey=SecurityGroupId,ParameterValue=sg-xxxxxxx \
ParameterKey=EC2KeypairName,ParameterValue=ssh_keypair \
--capabilities CAPABILITY_IAM \
--role-arn arn:aws:iam::0123456789012:role/CFAdmin
--region eu-west-1
```

Before deleting the CloudFormation stack, you must delete all the objects in the S3 buckets and buckets should be empty. Otherwise, AWS CloudFormation won't be able to delete the bucket.