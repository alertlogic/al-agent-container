# Kubernetes al-agent-container Deplyoment

Deploying the al-agent-container for IDS on a Kubernetes cluster is accomplished using a _DaemonSet_ in Kubernetes.  A _DaemonSet_ ensures that one al-agent-container is running on each physical node in the cluster.

Within this folder, you will find _al-agent-container.yaml_.   This is the YAML definition for the Kubernetes _DaemonSet_ to deploy the agent.

To deploy this agent to your cluster:

1. Ensure that kubectl is talking to the proper Kubernetes cluster.  ```kubectl get pods```
2. Edit al-agent-container.yaml and replace "your_registration_key_here" with your unique registration key.
3. Run the following command: ```kubectl apply -f al-agent-container.yaml```

To verify agent deployment and operation:

1. Confirm that the _DaemonSet_ is defined: ```kubectl describe daemonset al-agent-container```
2. Confirm that the _Pod_ is defined: ```kubectl get pods```
3. Pick one of the _al-agent-container_ pods, and run: ```kubectl logs -f <pod name>```
