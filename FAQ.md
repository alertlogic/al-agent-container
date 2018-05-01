# Frequently Asked Questions

### Will I need to add the registration key to the defined DaemonSet, TaskDefinition or the Docker Run example?
If the Alert Logic Cross-Account role is enabled as defined in the Prerequisites, the claiming functionality will execute without defining the registration key in the DaemonSet, TaskDefinition or the Docker Run example.

### What is the system resource demands for the al-agent-container and will they remain the same or shrink?
The current specification for the CPU and RAM limits are defined in the example Kubernetes DaemonSet and ECS TaskDefinition.  It is recommended that you use the resource limiting capability based on the defined value; otherwise, you run the risk of the al-agent-container not being bound by the constructs of the limits.  Overtime, we will improve the usage, size and performance capability of the al-agent-container and at the present time the requirements are set as follows:

- CPU Requests:  0.25
- Memory Requests:  100Mi
- CPU Limits:  3
- Memory Limits:  500Mi

### How do we stay up to date with the latest al-agent-container releases?
The al-agent-container is released frequently to DockerHub (https://hub.docker.com/r/alertlogic/al-agent-container/).  The preferred method for staying up to date would be for you to reference the *:latest* version tag in your specification file.

- image: alertlogic/al-agent-container:latest
