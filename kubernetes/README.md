# Deploy the Alert Logic Agent Container for Kubernetes

To deploy the Alert Logic Agent Container on a Kubernetes cluster, you must use a `DaemonSet`, which ensures that one Agent Container runs on each physical node in the Kubernetes cluster.

This directory contains the following files to install via helm, which are the YAML definitions you need to download and edit. These files allow the Kubernetes `DaemonSet` to deploy the agent. Go into the values.yaml to update the alertlogic.type attribute to the various cluster sockets
- `docker`: for Kubernetes clusters running Docker (mounts ```/var/run/docker.sock```)
- `containerd`: for Kubernetes clusters running containerd (mounts ```/run/containerd/containerd.sock```)
- `crio`: for Kubernetes/OpenShift clusters running CRI-O (mounts ```/run/crio/crio.sock```)

When upgrading the cluster to a different container engine, the version targeting the current container engine needs to de deleted (`helm uninstall {release_name}`), followed by deployment of the version targeting the new engine (see below). It is recommended to do this after the upgrade.

## Before You Begin
- You must have the kubectl command line interface installed and get authentication credentials to interact with the cluster where you want to install the Agent Container.
- You must have the helm command line interface installed
- Download this repository
- To deploy the Alert Logic Agent Container for Kubernetes, you need your unique registration key unless the deployment is set up for automatic provisioning.

**To find your unique registration key (MDR platform -- Data Center deployments only):**
1. In the Alert Logic console, navigate to Configure > Deployments section.
2. Click on the Data Center deployment.
3. Click Configuration Overview > Installation Instructions.
4. Copy your unique registration key for the appropriate network. The network must have a subnet with an IP range matching the agent's base host IP address(es), otherwise agent provisioning will fail.

**To find your unique registration key (Cloud Defender platform):**
1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

## Deploy the Agent Container
**To deploy the Agent Container to your cluster:**
1. Edit the chosen YAML file to replace "your_registration_key_here" with the unique registration key. Note that supported cloud deployments with valid credentials do not require registration keys, as provisioning is performed based on cloud metadata gathered by the agent and the Alert Logic back end. When using a supported cloud deployment, the `KEY` environment variable should be undefined.
2. In the command line, type  ```kubectl get pods``` to ensure kubectl communicates with the proper Kubernetes cluster.
3. In the command line, type ```helm install {release_name} .```.

**To verify agent deployment and operation:**
1. In the command line, type ```kubectl describe daemonset al-agent-container``` to confirm the DaemonSet definition.
2. In the command line, type ```kubectl get pods``` to confirm the Agent Container pod is running on every expected host in your cluster.
3. In the command line, type ```kubectl logs -l app=al-agent-container``` to confirm the Agent Container was registered successfully. Example of successful logs:

```
Oct 14 02:35:55 2018 al-agent[5]: ALC00083I [alc_config_unregister] clean up registration artefacts
Oct 14 02:35:55 2018 al-agent[5]: ALC00710I [alc_host_detect_type] No known VM environments detected; local host appears to be standalone
Oct 14 02:35:55 2018 al-agent[5]: ALC00080I [alc_config_provision_host] Fetched host certificate "/var/alertlogic/etc/host_crt.pem", "/var/alertlogic/etc/host_key.pem"

*** tmhost.conf config ***

source_config {
  source_id: "2E2A95A1-7827-1005-B5A0-0050568532D4"
  host_id: "2E132DBA-7827-1005-8BD0-0050568525D9"
  tm_config {
    encrypt: false
    compress: false
    udp: false
    packet_size: 0
  }
}
config_version: "958dc91f93dfee2b00ad99556d2c365a702115ff"

*** tmhost.conf config ***

source_config {
  source_id: "2E2A95A1-7827-1005-B5A0-0050568532D4"
  host_id: "2E132DBA-7827-1005-8BD0-0050568525D9"
  tm_config {
    encrypt: false
    compress: false
    udp: false
    packet_size: 0
    appliance_assignment {
      appliance_id: "ABE1B254-7827-1005-8473-0050568505BC"
      appliance_address {
        address: "10.138.0.12"
        port: 443
      }
    }
  }
}
```
