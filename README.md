# Alert Logic Agent Container

The Alert Logic Agent Container (al-agent-container) is a self-contained container image that is deployed into your container ecosystem to provide Network Intrusion Detection (Alert Logic Threat Manager) and Container Application Log Collection (Alert Logic Log Manager) services that tie back to Alert Logic product offerings.  This repository highlights supported platforms, provides example deployment documentation, FAQs and links to get you started down the right path for deploying Alert Logic Container Security Solution.

# General Requirements

Support for Alert Logic Agent Container Network Intrusion Detection requires the following:
* The environment **MUST** allow the al-agent-container to run in [privileged mode](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)
* The environment **MUST** allow the [mounting of docker.sock](https://docs.docker.com/storage/volumes/) through the volume mounting capability in Docker

Support for Alert Logic Agent Container Log Management requires the following:
* Default Docker logging driver enabled where the [default is json-file](https://docs.docker.com/config/containers/logging/configure/)

# Prerequisites

Before you deploy the Alert Logic Agent Container, you must:
- Deploy and activate the Threat Manager appliance
- Create a deployment in the Alert Logic console

The documentation in this repository helps you deploy the Threat Manager agent as a container. The documentation covers only the deployment of the agent. For more information about creating a deployment in the Alert Logic console, see the following:

- Get Started with the Alert Logic Cloud Defender Suite: https://docs.alertlogic.com/gsg/get-started-cloud-defender.htm
- How to Create and Manage Deployments: https://docs.alertlogic.com/userGuides/deployments.htm

If you have not done so already, follow the instructions in the link below to deploy and configure Threat Manager. Please note that any reference to the installation of the Threat Manager Agent should be replaced with deployment of the Alert Logic Agent Container as specified within this repository.

- Get Started with Alert Logic Threat Manager: https://docs.alertlogic.com/gsg/get-started-threat-manager.htm

# Image repository

Link : https://hub.docker.com/r/alertlogic/al-agent-container/
