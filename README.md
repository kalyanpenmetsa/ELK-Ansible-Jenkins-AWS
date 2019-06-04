![Logo of the project](https://t1.daumcdn.net/cfile/tistory/99DDB3495C5301B030)

# AWS - JENKINS - ANSIBLE - ELK
> An ELK installation pipeline using Jenkins on AWS

This project is designed to use AWS Cloud Formation template to create a Jenkins EC2 Instance which is responsible for creation, installation and configuration of Elastic-Logstash-Kibana stack on AWS.

## Jenkins EC2 Instance Creation using Cloud Formation

The repository contains the Cloud Formation Template (jenkins-ec2.yml) which can be used to create a Jenkins Cloud Formation Stack using both the AWS Console and AWS CLI as shown below. (You must have [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) installed on your local prior to the below step.)

```shell
aws cloudformation deploy --template-file {gitRepoDIR}/jenkins-ec2.yml --stack-name jenkins-stack --region us-east-2 --capabilities CAPABILITY_NAMED_IAM
```

The above command will create a security group, create an IAM Role, Add Role policies to the IAM Role, create an Instance profile and finally create an t2.micro EC2 instance.

Jenkins console: http://{publicDNS}:8080

## Jenkins Job Run to create EC2 Instance and Install ELK Stack

All the necessary Jenkins plugin and security credentials required are included in the prepackaged Jenkins AMI which was used to create Jenkins master in the above step.

The jenkins pipeline to create EC2 instance and execute ansible playbooks is part of the code repository. Below are the instructions to execute the Jenkins job (http://{publicDNS}:8080/job/ELK-Ansible-Jenkins-AWS/).

```shell
Step1: Go to the above mentioned Jenkins Job by replacing {publicDNS} placeholder with your current Jenkins master.
Step2: Click on "Scan Repository Now" on the left hand panel of the job to scan the repository for Jenkinsfile.
```

The above step will kickstart the jenkins job which will create an EC2 Instance using AWS CLI, gather the private IP address of the EC2 Instance and execute ansible playbooks to create ELK stack on the EC2 Instance. I have selected t2.micro Redhat AMI. These ansible playbooks are platform independent, they can run on Redhat as well as Debian flavored Instances. Jenkins job will receive security group Info, subnet ID, AMI ID, keypair ID Info from cloud formation userdata.
Please note that the EC2 instance should atleast be t2.medium as ELK stack requires minimum of 2GB RAM.

## Post Deployment

Once the deployment is completed, Elasticsearch, Logstash and Kibana applicatipns are installed, configured, and available to use. You can access kibana dashboard using the following URL.

Kibana Dashboard: http://{publicDNS}:5601

## Results:

![GitHub Logo](/images/jenkins-console.png)

## Author Information

This project is developed by Venkat Kalyan Penmetsa as part of REAN Cloud assessment test.
