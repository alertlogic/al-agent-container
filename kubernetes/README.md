# Deploy the Alert Logic Agent Container for Kubernetes

To deploy the Alert Logic Agent Container on a Kubernetes cluster, you must use a `DaemonSet`, which ensures that one Agent Container runs on each physical node in the Kubernetes cluster.

This directory contains the al-agent-container.yaml file, which is the YAML definition you need to download and edit. This file allows the Kubernetes `DaemonSet` to deploy the agent.

## Before You Begin
- You must have the kubectl command line interface installed and get authentication credentials to interact with the cluster where you want to install the Agent Container.
- Download the al-agent-container.yaml file.
- To deploy the Alert Logic Agent Container for Kubernetes, you need the unique registration key for your Alert Logic account.

**To find your unique registration key:**
1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

**Please Note:** Unique registration keys aren't required for Cloud Defender/SIEMless deployments on AWS and Azure. Please delete the relevant block as required.

## Deploy the Agent Container
**To deploy the Agent Container to your cluster:**
1. Edit the al-agent-container.yaml file to replace "your_registration_key_here" with the unique registration key for your Alert Logic Account
2. In the command line, type  ```kubectl get pods``` to ensure kubectl communicates with the proper Kubernetes cluster.
3. In the command line, type ```kubectl apply -f al-agent-container.yaml```.

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
