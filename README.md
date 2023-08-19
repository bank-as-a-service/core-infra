# core-infra
Cloudformation script for the one and only kubernetes runtime

## Pre-requisites
- AWS CLI

## Connecting to the Kubernetes API
```shell
aws eks --region us-west-2 update-kubeconfig --name BankCoreCluster-EKS
```

## How to deploy
- create the certificate for the ALB
```shell
aws cloudformation create-stack \
  --region us-west-2 \
  --stack-name BankCoreCertificate \
  --template-body file://certificate.yaml \
  --parameters ParameterKey=HostedZoneDomainName,ParameterValue=example.com ParameterKey=HostedZoneId,ParameterValue=ZXXXXXXXXXXXX
```

- create network layer
```shell
aws cloudformation create-stack \
  --region us-west-2 \
  --stack-name BankCoreNetwork \
  --template-body file://network.yaml \
  --parameters ParameterKey=AvailabilityZone1,ParameterValue=us-west-2a ParameterKey=AvailabilityZone2,ParameterValue=us-west-2b ParameterKey=AvailabilityZone3,ParameterValue=us-west-2c
```

- create kubernetes layer
```shell
aws cloudformation create-stack \
  --region us-west-2 \
  --stack-name BankCoreCluster \
  --template-body file://kubernetes.yaml \
  --parameters ParameterKey=VPCStackName,ParameterValue=BankCoreNetwork \
  --capabilities CAPABILITY_NAMED_IAM
```

- create the ALB for ingress
```shell
aws cloudformation create-stack \
  --region us-west-2 \
  --stack-name BankCoreIngressALB \
  --template-body file://alb.yaml \
  --parameters ParameterKey=VPCStackName,ParameterValue=BankCoreNetwork
```

## How to update
- update network layer
```shell
aws cloudformation update-stack \
  --region us-west-2 \
  --stack-name BankCoreNetwork \
  --template-body file://network.yaml \
  --parameters ParameterKey=AvailabilityZone1,ParameterValue=us-west-2a ParameterKey=AvailabilityZone2,ParameterValue=us-west-2b ParameterKey=AvailabilityZone3,ParameterValue=us-west-2c
```
- update kubernetes layer
```shell
aws cloudformation update-stack \
  --region us-west-2 \
  --stack-name BankCoreCluster \
  --template-body file://kubernetes.yaml \
  --parameters ParameterKey=VPCStackName,ParameterValue=BankCoreNetwork \
  --capabilities CAPABILITY_NAMED_IAM \
  --disable-rollback
```


## How to delete
-- delete the ALB for ingress
```shell
aws cloudformation delete-stack \
  --region us-west-2 \
  --stack-name BankCoreIngressALB
```

- delete kubernetes layer
```shell
aws cloudformation delete-stack \
  --region us-west-2 \
  --stack-name BankCoreCluster
```
- delete network layer
```shell
aws cloudformation delete-stack \
  --region us-west-2 \
  --stack-name BankCoreNetwork
```
