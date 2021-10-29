# SonarQube on AWS
This repo contains a CloudFormation template to deploy SonarQube on Amazon Web Services (AWS).

It uses the official [container images release by SonarSource](https://hub.docker.com/_/sonarqube). The configuration is done according to [the SonarQube installation docs](https://docs.sonarqube.org/latest/setup/install-server/#header-4).

## Prerequisites
The template requires an existing VPC with at least two subnets. 

Either reuse an existing VPC or follow the steps in the [ECS userguide](https://docs.aws.amazon.com/AmazonECS/latest/userguide/create-public-private-vpc.html) to create one. You can also use the [VPC Quick Start](https://aws.amazon.com/quickstart/architecture/vpc/).

## Deployment
The deployment has been tested in the AWS regions `eu-central-1` and `eu-north-1` but also works in other regions where the used services are available. 

Go to the [AWS CloudFormation Console](https://console.aws.amazon.com/cloudformation/home?#/stacks/create/template) to start the deployment or use the AWS CLI with a command similar to this one: 

    aws cloudformation create-stack --stack-name SonarQube --template-body file://path/to/directory/sonarqube.yaml --capabilities CAPABILITY_NAMED_IAM --parameters ParameterKey=VPC,ParameterValue=vpc-00id00 ParameterKey=Subnet1,ParameterValue=subnet-00id00 ParameterKey=Subnet2,ParameterValue=subnet-00id00

After stack creation is complete look for the output with the key `LoadBalancerUrl` and open the page in a browser. Login with user `admin` and change the password.

## FAQ

### Is it ready for production?
The template should be considered as a sample.

### How to use HTTPS instead of HTTP?
This requires a certificate. Have a look at the areas that are commented out in the template to add your certificate and setup a Route53 recordset.

### Why isn't there a proper auto-scaling configuration for the container?
Only one running instance must write to the database. High availability and cluster scalability are features of the [SonarQube Data Center Edition](https://docs.sonarqube.org/latest/setup/operate-cluster/).

### Why RDS for PostgreSQL and not Aurora Serverless or another serverless database like DynamoDB?
PostgreSQL is the only Open Source database that SonarQube support. As of now the current config is a cheaper way of running this than Aurora Serverless, but it's possible to change it.

### Why is everything put in just two subnets?
It's possible to add a third subnet for another Availability Zone. It's also common to setup separate subnets for e.g. the database. 
As written above this template should be considered as a simplified sample.

### Can I use Amazon OpenSearch Service instead of the integrated Elasticsearch?
Currently SonarQube only supports the bundled Elasticsearch version. See [posting 1](https://community.sonarsource.com/t/sonarqube-8-5-community-edition-external-elasticsearch-setup/34346),
[posting 2](https://community.sonarsource.com/t/sonarqube-fargate-aws-elasticsearch/11576/12) and
[posting 3](https://community.sonarsource.com/t/support-external-elasticsearch-cluster/47770) in the SonarSource community.