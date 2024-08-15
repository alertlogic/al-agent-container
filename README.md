# Alert Logic Agent Container

The Alert Logic Agent Container (al-agent-container) is a self-contained container image you deploy into your containerized workloads to provide network intrusion detection and container application log collection services by Alert Logic Threat Manager and Alert Logic Log Manager. 

This repository lists supported platforms, provides deployment documentation (including examples), FAQs, and links that help you deploy the Alert Logic Agent Container. For more information, see [www.alertlogic.com](https://www.alertlogic.com).

# Prerequisites

Before you deploy the Alert Logic Agent Container, you must:
- Deploy and activate the Threat Manager appliance.
- Create a deployment in the Alert Logic console.

The documentation in this repository helps you deploy the Threat Manager agent as a container. The documentation covers only the deployment of the agent. For more information about creating a deployment in the Alert Logic console, see the following:

- [Get Started with the Alert Logic Cloud Defender Suite](https://legacy.docs.alertlogic.com/gsg/get-started-cloud-defender.htm)
- [How to Create and Manage Deployments](https://legacy.docs.alertlogic.com/userGuides/deployments.htm)

If you have not done so already, follow the instructions in the link below to deploy and configure Threat Manager. 

- [Get Started with Alert Logic Threat Manager](https://legacy.docs.alertlogic.com/gsg/get-started-threat-manager.htm)

**Note:** As you read "Get Started with Alert Logic Threat Manager," replace references to the installation of the Threat Manager Agent with deployment of the Alert Logic Agent Container, as specified in this repository.

# Requirements

**Support for Alert Logic Agent Container Network Intrusion Detection requires the following:**
- The environment must allow the al-agent-container to run in [privileged mode](#privileged_mode).
- The environment must allow the [mounting of ```docker.sock```](https://docs.docker.com/storage/volumes/), ```containerd.sock```, or ```crio.sock``` through the volume mounting capability in your container engine.

Environments that do not permit the above (e.g. [AWS Fargate](fargate/README.md)) usually permit running Alert Logic Agent Container as a sidecar in each task or pod instead, where the agent still has access to that task or pod's network interfaces. Environments that permit privileged mode but do not expose ```docker.sock```, ```containerd.sock```, or ```crio.sock``` are not fully supported. While Alert Logic Agent Container can still be installed in privileged host network mode to capture syslog and network traffic on base host network interfaces, it does not discover other containers running on the host, capture traffic from their virtual network interfaces, or collect their native (stdout/stderr) logs.

**Support for Alert Logic Agent Container Log Management requires the following:**
- The environment must allow the [mounting of ```docker.sock```](https://docs.docker.com/storage/volumes/), ```containerd.sock```, or ```crio.sock``` through the volume mounting capability in your container engine, and Alert Logic Agent Container must be running in host network mode to access the container engine's `logs` or `attach` API.
- Alternatively, container logs must be routed to Alert Logic Agent Container's syslog port (TCP or UDP 1514 by default) using third-party software, for example AWS FireLens and Fluent Bit (see [Fargate README](fargate/README.md)), in a format expected by Alert Logic.

# Volumes

Alert Logic Agent Container exposes its Fluent Bit launcher in a mountable volume (`/var/alertlogic/lib/fluent-bit`) so that Fluent Bit containers can be started and configured from the command line. The volume is only needed if Fluent Bit is used for container log routing (see [Fargate README](fargate/README.md)). In all other cases it can be removed.

# Alert Logic Agent Container vs. Alert Logic Agent for Linux

Alert Logic Agent Container is the supported container packaging of the [Alert Logic Agent for Linux](https://docs.alertlogic.com/prepare/alert-logic-agent-linux.htm). You can use it for convenient deployment of the Alert Logic Agent in containerized environments. It does not provide any different functionality compared to a normal Alert Logic Agent for Linux installation on the base host. This includes container traffic monitoring and container log collection, which both work the same way in either deployment mode. Alert Logic Agent Container and Alert Logic Agent for Linux must not both be installed on the same host. Doing so causes the installations to conflict.

The Flat File log collection, File Integrity Monitoring (FIM), and agent-based scanning features of the Alert Logic Agent are not currently container-aware. This means they are ineffectual if started inside a container (unless relevant file system paths are exposed to the Alert Logic Agent Container). For deployments using these features, it is recommended to install the Alert Logic Agent for Linux on the base host (where the operating system permits). For all other deployments, it is recommended to use Alert Logic Agent Container.

# <a name="privileged_mode"></a> Privileged Mode

Alert Logic Agent Container requires [privileged mode](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) (superuser access) to enter other containers’ network namespaces and open their network adapters for capture. This is the same level of access used by Alert Logic Agent for Linux, and enables running only one copy of the Alert Logic Agent Container per container instance (instead of running it as a sidecar in each task or pod).

Where this is a compliance issue, the following individual [superuser capabilities](https://man7.org/linux/man-pages/man7/capabilities.7.html) (`--cap-add`) can be currently used instead of privileged mode:
- `SYS_ADMIN` (required to enter other containers' namespaces)
- `SYS_PTRACE` (required to enter other containers' namespaces)
- `NET_ADMIN` (required to capture traffic from network adapters)
- `NET_BIND_SERVICE` (required to listen on network ports lower than 1024, if configured by log policy)

However, future versions of the Alert Logic Agent might require additional capabilities. This could break your deployment automation and existing Alert Logic Agent Container deployments following a remote update. Therefore, if you choose to restrict Alert Logic Agent Container's capabilities in this way, you do so at your own risk.

# Image Repositories

- Docker Hub: https://hub.docker.com/r/alertlogic/al-agent-container
- Amazon ECR Public: https://gallery.ecr.aws/alertlogic/al-agent-container

# Pull Rate Limits

Using the ECR repository may be preferred over Docker Hub for large-scale workloads. Depending on workload location and external IP configuration, ECR may afford higher pull rate limits (and shorter cool-down periods) unless a paid Docker subscription is used. In both cases, authenticated pulls receive higher rate limits than unauthenticated ones. For the current pull rate limits, refer to:

- Docker Hub: https://docs.docker.com/docker-hub/download-rate-limit/
- Amazon ECR Public: https://docs.aws.amazon.com/AmazonECR/latest/public/public-service-quotas.html

If the rate limits afforded by either service are not sufficient for your needs, consider mirroring Alert Logic Agent Container repository into your own private repository using [ECR Pull-through Cache](https://docs.aws.amazon.com/AmazonECR/latest/userguide/pull-through-cache.html) or a similar service, then using the mirror to deploy your workloads.
