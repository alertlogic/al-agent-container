# Frequently Asked Questions

### Do I need to add the registration key to the defined DaemonSet, TaskDefinition or the Docker Run example?
If you enable the Alert Logic cross-account role as defined in the Prerequisites, the claiming functionality executes without the need to define the registration key in the DaemonSet, TaskDefinition or the Docker Run example.

### What are the system resource demands for the al-agent-container, and will they remain the same or shrink in the future?
The current specification for the CPU and RAM limits are defined in the example Kubernetes DaemonSet and ECS TaskDefinition. Alert Logic recommends that you use the resource limiting capability based on the defined value; otherwise, the al-agent-container may not be bound by the constructs of the limits. Over time, Alert Logic will improve the usage, size and performance capability of the al-agent-container, and currently the requirements are set as follows:

- CPU Requests:  0.25
- Memory Requests:  100Mi
- CPU Limits:  3
- Memory Limits:  500Mi

### How do I stay up to date with the latest al-agent-container releases?
Alert Logic releases al-agent-container frequently to DockerHub (https://hub.docker.com/r/alertlogic/al-agent-container/). The preferred method for staying up to date is to reference the *:latest* version tag in your specification file.

- image: alertlogic/al-agent-container:latest
