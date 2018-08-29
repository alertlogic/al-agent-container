# General Requirements

Support for Alert Logic Agent Container Network Intrusion Detection requires the following:
* The environment **MUST** allow the al-agent-container to run in [privileged mode](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)
* The environment **MUST** allow the [mounting of docker.sock](https://docs.docker.com/storage/volumes/) through the volume mounting capability in Docker

Support for Alert Logic Agent Container Log Management requirements the following:
* Default Docker logging driver enabled where the [default is json-file](https://docs.docker.com/config/containers/logging/configure/)

# Supported Orchestration and Operating Environments

The following orchestration and operating environments are supported:

Amazon Web Services | Microsoft Azure | On-Premise
------------------- | --------------- | ----------
AWS EKS | Azure AKS | Kubernetes
AWS ECS | Kubernetes ACS-Engine | CoreOS
AWS Elastic Beanstalk Multicontainer Deployments | CoreOS on Azure |
Kubernetes deployed on AWS EC2 instances | |
CoreOS deployed on AWS EC2 instances | |
