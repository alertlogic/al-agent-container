# Supported Infrastructure Platforms

Alert Logic supports deployment of the Alert Logic Agent Container on **Amazon Web Services**, **Microsoft Azure** and **On-Premise** environments.  No other cloud providers or platforms are supported at this time.

# General Requirements

Support for Alert Logic Agent Container Network Intrusion Detection requires the following:
* The environment **MUST** allow the al-agent-container to run in [privileged mode](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)
* The environment **MUST** allow the [mounting of docker.sock](https://docs.docker.com/storage/volumes/) through the volume more capability in Docker

Support for Alert Logic Agent Container Log Management requirements the following:
* Default Docker logging driver enabled where the [default is json-file](https://docs.docker.com/config/containers/logging/configure/)
