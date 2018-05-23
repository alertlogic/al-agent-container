# Deploy the Alert Logic Agent Container for Kubernetes

To deploy the Alert Logic Agent Container on a Kubernetes cluster, you must use a `DaemonSet`, which ensures that one Agent Container runs on each physical node in the Kubernetes cluster.

This directory contains the al-agent-container.yaml file, which is the YAML definition you need to download and edit. This file allows the Kubernetes `DaemonSet` to deploy the agent.

## Before You Begin
- You must have the kubectl command line interface installed and pointed to the cluster to which you want to install the Agent Container.
- Download the al-agent-container.yaml file and the netpol_agent_metadata.yaml file. 
- To deploy the Alert Logic Agent Container for Kubernetes, you need your unique registration key. 

**To find your unique registration key:**
1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

## Deploy the Agent Container 
**To deploy the Agent Container to your cluster:**
1. Edit the al-agent-container.yaml file to replace "your_registration_key_here" with your unique registration key.
2. In the command line, type  ```kubectl get pods``` to ensure kubectl communicates with the proper Kubernetes cluster.
3. In the command line, type ```kubectl apply -f al-agent-container.yaml```.

**To verify agent deployment and operation:**
1. In the command line, type ```kubectl describe daemonset al-agent-container``` to confirm the DaemonSet definition.
2. In the command line, type ```kubectl get pods``` to confirm the Agent Container pod is running on every expected host in your cluster.
3. In the command line, in one of the pods, type ```kubectl logs -f <pod name>``` to confirm the Agent Container appears on the list. 

## Configure the Network Policy Exception in AWS

If you use Calico or Weave as a container network interface (CNI), your default network policy could deny the pods access to the AWS EC2 metadata. To avoid this situation, you must add a network policy exception that allows Agent Container pods to access EC2 instance metadata.

**To create a new network policy:**

1. Edit the netpol-al-agent-container.yaml file to replace the namespace value with that of your deployment namespace.
2. In the command line, type ```kubectl create -f netpol_agent_metadata.yaml```.
