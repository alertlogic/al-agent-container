# Alert Logic Agent Container

The Alert Logic Agent Container (al-agent-container) is a self-contained container image you deploy into your containerized workloads to provide network intrusion detection and container application log collection services by Alert Logic Threat Manager and Alert Logic Log Manager. 

This repository lists supported platforms, provides deployment documentation (including examples), FAQs, and links that help you deploy the Alert Logic Agent Container. For more information, see [www.alertlogic.com](https://www.alertlogic.com).

# Prerequisites

Before you deploy the Alert Logic Agent Container, you must:
- Deploy and activate the Threat Manager appliance.
- Create a deployment in the Alert Logic console.

The documentation in this repository helps you deploy the Threat Manager agent as a container. The documentation covers only the deployment of the agent. For more information about creating a deployment in the Alert Logic console, see the following:

- [Get Started with the Alert Logic Cloud Defender Suite](https://docs.alertlogic.com/gsg/get-started-cloud-defender.htm)
- [How to Create and Manage Deployments](https://docs.alertlogic.com/userGuides/deployments.htm)

If you have not done so already, follow the instructions in the link below to deploy and configure Threat Manager. 

- [Get Started with Alert Logic Threat Manager](https://docs.alertlogic.com/gsg/get-started-threat-manager.htm)

**Note:** As you read "Get Started with Alert Logic Threat Manager," replace references to the installation of the Threat Manager Agent with deployment of the Alert Logic Agent Container, as specified in this repository.

# Requirements

**Support for Alert Logic Agent Container Network Intrusion Detection requires the following:**
- The environment must allow the al-agent-container to run in [privileged mode](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities).
- The environment must allow the [mounting of ```docker.sock```](https://docs.docker.com/storage/volumes/) through the volume mounting capability in Docker.

**Support for Alert Logic Agent Container Log Management requires the following:**
- Enable the default Docker logging driver, where the [default is ```json-file```](https://docs.docker.com/config/containers/logging/configure/).

# Image repository

Link : https://hub.docker.com/r/alertlogic/al-agent-container/
