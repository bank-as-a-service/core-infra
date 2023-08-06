# Setup Flux

## Pre-requisites
- AWS CLI
```shell
aws eks --region us-west-2 update-kubeconfig --name BankCoreCluster-EKS
```
- kubectl
```shell
kubectl get nodes
```
- github credentials
```shell
export GITHUB_TOKEN=<your-token>
export GITHUB_USER=<your-username>
```
- fluxctl
```shell
brew install fluxcd/tap/flux
flux check --pre
```

## Installation
```shell
flux bootstrap github \
  --owner=bank-as-a-service \
  --repository=core-infra-flux \
  --branch=main \
  --path=./clusters/BankCoreCluster \
  --personal
```