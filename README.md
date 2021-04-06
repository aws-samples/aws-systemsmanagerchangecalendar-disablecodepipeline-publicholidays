# Automate suspension of AWS CodePipeline release during critical events using AWS Systems Manager Change Calendar 

This repository contains the artifact samples related to this [blog](URL) published on the [AWS Infrastructure & Automation blog](https://aws.amazon.com/blogs/infrastructure-and-automation/)

## Overview

This is a sample solution using AWS Systems Manager Change Calendar to suspend the AWS CodePipeline during critical public holidays. 

Following is the structure of this repository
```
 aws-systemsmanagerchangecalendar-disablecodepipeline-publicholidays
    ├── Automation Document
    │   └── SuspendCodePipeline.yml
    ├── DeploySimplePipelineS3Bucket
    │   ├── README.md
    │   └── DeploySimplePipelineS3Bucket.yaml
    ├── LICENSE
    ├── Public calendars samples
    │   ├── IrishHolidays2021_AWS.ics
    │   ├── UsPublicHolidays2021_AWS.ics
    │   └── WeekendsCalendar_AWS.ics
    ├── CODE_OF_CONDUCT.md
    ├── CONTRIBUTING.md
    └── README.md
```

## Automation Document

 This directory automation document enables/disables the transition of the pipelines stages created in AWS CodePipeline using the following parameters:

  1. **AutomationAssumeRole**: IAM role to execute the Automation document.
  2. **PipelineName**: Name of the pipeline for which you want to enable or disable transitions.
  3. **DesiredState**: Whether to enable or disable transition of the artifacts.
  4. **StageName**: Name of the stage where you want to modify the inbound or outbound transition of artifacts.
  5. **TransitionType**: Specifies whether artifacts are prevented from transitioning into the stage and being processed by the actions in that stage (inbound) or prevented from transitioning from the stage after they have been processed by the actions in that stage (outbound).
  6. **Reason**: Reason for modifying the transition state. 

## DeploySimplePipelineS3Bucket

This directory contains the sample AWS CloudFormation template that deploys a two-stage pipeline that uses a versioned S3 bucket and CodeDeploy to release a sample application as explained in AWS CodePipeline [tutorials](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-simple-s3.html). The application is deployed on EC2 instance using AWS Code-Deploy agent and this agent is installed by EC2 user-data script. 

Once the stack is deployed successfully, user will be able to access the deployed sample application using the Public DNS name of the launched EC2 instance. Following are the input parameters used by this template:

1.	**CodePipelineName** – Name of the pipeline to be created
2.	**PublicSubnetId** -- SubnetID with Internet Access. This access is needed as EC2 instances, created by this template, requires AWS CodeDeploy agent & it's dependencies to be installed. 
3.	**SecurityGroupId** – The Security Group ID to attach with the instance. This Security Group must allow Outbound internet access on port 80 & 443 and Inbound access on port 80 from your source machine to access the deployed sample application.
4.	**EC2KeypairName** – Name of EC2 KeyPair
5.	**AmiId** – AMI ID to launch the instance. By default, AmazonLinux 2 AMI is used.

Refer to [README](https://github.com/aws-samples/aws-systemsmanagerchangecalendar-disablecodepipeline-publicholidays/blob/main/DeploySimplePipelineS3Bucket/README.md) for usage instructions.

## Public Calendar samples

This directory contains the following public calendars that can be used as samples to import public calendars into AWS Systems Manager Change Calendar. 

1. Ireland Holidays 2021
2. US Holidays 2021
3. Weekends 

These calendars can be imported as AWS Systems Manager Change Calendar document using the using the following command, where ``SampleHolidays2021_AWS.ics`` contains the content from one of the [samples](https://github.com/aws-samples/aws-systemsmanagerchangecalendar-disablecodepipeline-publicholidays/tree/main/Public%20calendars%20samples):

```
aws ssm create-document --name PublicHolidays \
--content file://SampleHolidays2021_AWS.ics \
--document-type ChangeCalendar --document-format TEXT \
--region eu-west-1
```

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.