## Deploy the Alert Logic Agent Container for AWS Fargate
You can deploy the Alert Logic Agent Container in Amazon Elastic Container Service (ECS) environments that run Amazon Web Services (AWS) Fargate.

Use these instructions for environments running Amazon ECS tasks with the Fargate launch type. To deploy the Alert Logic Agent Container for Amazon ECS tasks with the EC2 launch type, see [ECS README](../ecs/README.md) instead. 

## AWS Fargate Support
To protect environments that use Fargate with Amazon ECS, the required method is to deploy the Alert Logic Agent Container as a sidecar in each Fargate ECS task. With this method, the Alert Logic agent can still access the network interfaces of that task. Alert Logic collects network traffic and log messages from a specific task without violating the integrity of other customer environments in the AWS Fargate cluster.

For Alert Logic to fully integrate with a container environment, the Docker socket must be mounted through the volume mounting capability in Docker, which the Fargate environment does not allow. For this reason, Alert Logic can protect containers within a Fargate task but does not discover other containers running on the host or capture traffic from their virtual network interfaces.

## Agent Container for Fargate for Managed Detection and Response Customers

### Deploy Agent Container 

Complete the following steps to protect your AWS Fargate deployments:

1. Modify your AWS Fargate task definition.

You must run the Alert Logic Agent Container and a supported [Fluent Bit](https://docs.fluentbit.io/manual) container (to direct logs) as sidecars to each Fargate ECS task. Using the JSON tab, append the following two container definitions to the ```containerDefinitions``` array within your existing Fargate ECS task definition. For all other containers in your task definition, set the log driver to `awsfirelens`:
   ```
   {
     "name": "al-agent",
     "image": "alertlogic/al-agent-container:latest",
   },
   {
     "name": "log_router",
     "image": "fluent/fluent-bit:latest",
     "firelensConfiguration": {"type": "fluentbit"},
     "volumesFrom": [{"sourceContainer": "al-agent"}],
     "dependsOn": [{
       "containerName": "al-agent",
       "condition": "START"
     }],
     "entryPoint": ["/var/alertlogic/lib/fluent-bit/fluent-launch"],
     "command": [
       "-s", "Scheduler.Cap", "60",
       "-i", "Mem_Buf_Limit", "50M",
       "--",
       "-F", "record_modifier",
       "-m", "*",
       "-p", "Record=pid 1",
       "-o", "syslog",
       "-m", "*",
       "-p", "Mode=tcp",
       "-p", "Host=127.0.0.1",
       "-p", "Port=1514",
       "-p", "Retry_Limit=False",
       "-p", "Syslog_Format=rfc3164",
       "-p", "Syslog_Maxsize=786432",
       "-p", "Syslog_Hostname_Key=container_id",
       "-p", "Syslog_Appname_Key=container_name",
       "-p", "Syslog_Procid_Key=pid",
       "-p", "Syslog_Message_Key=log"
     ]
   },
   {
     "name": "<application_container_1>",
     ...
     "logConfiguration": {"logDriver": "awsfirelens"}
   },
   {
     "name": "<application_container_2>",
     ...
     "logConfiguration": {"logDriver": "awsfirelens"}
   },
   ...
   ```
Alternatively, another option is to 'Create a New Revision' using the Builder and 'Add Container' with the above image.

It is recommended you set up security groups to disallow external or VPC traffic (including Alert Logic vulnerability scanners) to TCP or UDP port 1514 of the task (which is used by the Alert Logic Agent Container to listen for incoming logs). Otherwise, bogus logs might be generated.

For more information on configuring log routing with Fluent Bit, see [Advanced Log Routing](#log_routing).

2. Update the Service to use the latest Task Definition revision. 

The service tasks will now be updated and the agent will show in the Health section of the Alert Logic portal in due course. 


## Agent Container for Fargate for Cloud Defender Customers

### Before You Begin
For the Cloud Defender platform, you need your unique registration key to deploy the Alert Logic Agent Container for AWS Fargate. 

**To find your unique registration key:**
1. In the Alert Logic console, click the Support Information icon.
2. Within the 'Details' section, Copy your unique registration key.

### Deploy Agent Container 

Complete the following steps to protect your AWS Fargate deployments:

1. Modify your AWS Fargate task definition.

You must run the Alert Logic Agent Container and a supported [Fluent Bit](https://docs.fluentbit.io/manual) container (to direct logs) as sidecars to each Fargate ECS task. Using the JSON tab, append the following two container definitions to the ```containerDefinitions``` array within your existing Fargate ECS task definition. For all other containers in your task definition, set the log driver to `awsfirelens`:
   ```
   {
     "name": "al-agent",
     "image": "alertlogic/al-agent-container:latest",
     "environment": [
       {
         "name": "KEY",
         "value": "your_registration_key_here"
       }
     ]
   },
   {
     "name": "log_router",
     "image": "fluent/fluent-bit:latest",
     "firelensConfiguration": {"type": "fluentbit"},
     "volumesFrom": [{"sourceContainer": "al-agent"}],
     "dependsOn": [{
       "containerName": "al-agent",
       "condition": "START"
     }],
     "entryPoint": ["/var/alertlogic/lib/fluent-bit/fluent-launch"],
     "command": [
       "-s", "Scheduler.Cap", "60",
       "-i", "Mem_Buf_Limit", "50M",
       "--",
       "-F", "record_modifier",
       "-m", "*",
       "-p", "Record=pid 1",
       "-o", "syslog",
       "-m", "*",
       "-p", "Mode=tcp",
       "-p", "Host=127.0.0.1",
       "-p", "Port=1514",
       "-p", "Retry_Limit=False",
       "-p", "Syslog_Format=rfc3164",
       "-p", "Syslog_Maxsize=786432",
       "-p", "Syslog_Hostname_Key=container_id",
       "-p", "Syslog_Appname_Key=container_name",
       "-p", "Syslog_Procid_Key=pid",
       "-p", "Syslog_Message_Key=log"
     ]
   },
   {
     "name": "<application_container_1>",
     ...
     "logConfiguration": {"logDriver": "awsfirelens"}
   },
   {
     "name": "<application_container_2>",
     ...
     "logConfiguration": {"logDriver": "awsfirelens"}
   },
   ...
   ```
In the task definition, update the ```value``` of the ```KEY``` environment variable with your unique registration key. 

Alternatively, another option is to 'Create a New Revision' using the Builder and 'Add Container' with the above image. You must then add the environment variable ```KEY=<your_registration_key_here>```.

It is recommended you set up security groups to disallow external or VPC traffic (including Alert Logic vulnerability scanners) to TCP or UDP port 1514 of the task (which is used by the Alert Logic Agent Container to listen for incoming logs). Otherwise, bogus logs might be generated.

For more information on configuring log routing with Fluent Bit, see [Advanced Log Routing](#log_routing).

2. Update the Service to use the latest Task Definition revision. 

The service tasks will now be updated and the agent will show in the Protected Hosts section of the Alert Logic portal in due course.

## Agent Container for Fargate using AWS CLI

1. Update your existing task definition JSON file as per the MDR or Cloud Defender instructions above and use the following command in the AWS CLI:
   ```
   aws ecs register-task-definition --cli-input-json file://path//to/task-definition.json
   ```

2. Use your preferred method to deploy the updated task definition. For example, you can enter the following command in the AWS CLI and replace the `{service_using_task_definition}` and `{task_definition}` variables with valid values:
   ```
   aws ecs update-service --service {service_using_task_definition} --task-definition {task_definition}
   ```
For reference, see https://docs.aws.amazon.com/cli/latest/reference/ecs/update-service.html. 

## <a name="log_routing"></a> Advanced Log Routing

This section provides more technical detail about AWS FireLens log routing for Fargate ECS tasks using Fluent Bit. You can use this if you need to set up additional log outputs for your Fargate tasks (besides Alert Logic Agent Container), or perform any other Fluent Bit changes.

### Configuration and Data Path

1. Logs are directed from application containers to [AWS FireLens](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/firelens-taskdef.html) log pseudo-driver, which internally uses [Fluentd log driver](https://docs.docker.com/config/containers/logging/fluentd/).
2. This pseudo-driver directs logs in Fluentd record format via the Unix socket `/var/run/fluent.sock` into a container bearing the `firelensConfiguration` parameter (only one such container is allowed per task, in our case the `log_router` container running Fluent Bit).
3. Fluent Bit configuration file `/fluent-bit/etc/fluent-bit.conf` is generated by FireLens to output logs to various destinations according to the `firelensConfiguration` parameter in the Fluent Bit container and the `logConfiguration.options` paramaters (if specified) in each application container.
4. [Fluent Bit launcher](#fluent_launch), provided by a volume mounted from Alert Logic Agent Container, is used as an entry point in the `log_router` container.
5. The launcher further updates the FireLens-generated configuration to send a copy of the log output to the Alert Logic Agent Container's syslog collector (see [Syslog Output Plug-in Configuration](https://docs.fluentbit.io/manual/pipeline/outputs/syslog)) in the format expected by Alert Logic. This is in addition to any other log destinations already configured with FireLens.
6. The launcher runs Fluent Bit with this updated configuration.
7. Fluent Bit consumes logs from the log driver and outputs them to the configured destinations (including Alert Logic Agent Container) once they become available. It buffers up to 50 MB of logs in memory (configurable by `-i Mem_Buf_Limit <N>` launcher argument).

### Choosing a Fluent Bit Container Image

If no AWS log outputs (`firehose`, `cloudwatch`, or `kinesis`) are used in addition to the Alert Logic Agent Container, the recommended Fluent Bit image is the upstream `fluent/fluent-bit:latest` (as used in the recommended configuration) or `cr.fluentbit.io/fluent/fluent-bit:latest` (using Fluent Bit's own container repository instead of Docker Hub). This tends to be minimal in terms of memory usage, space usage, and download size.

If the above AWS log outputs are needed, or if ECR is preferred to Docker Hub, the [AWS for Fluent Bit](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/firelens-using-fluentbit.html) image can be used instead, for example:

   ```
   {
     "name": "log_router",
     "image": "public.ecr.aws/aws-observability/aws-for-fluent-bit:stable",
     "firelensConfiguration": {"type": "fluentbit"},
     "volumesFrom": [{"sourceContainer": "al-agent"}],
     "dependsOn": [{
       "containerName": "al-agent",
       "condition": "START"
     }],
     "entryPoint": ["/var/alertlogic/lib/fluent-bit/fluent-launch"],
     "command": [
       "-s", "Scheduler.Cap", "60",
       "-i", "Mem_Buf_Limit", "50M",
       "--",
       "-e", "/fluent-bit/firehose.so",
       "-e", "/fluent-bit/cloudwatch.so",
       "-e", "/fluent-bit/kinesis.so",
       "-F", "record_modifier",
       "-m", "*",
       "-p", "Record=pid 1",
       "-o", "syslog",
       "-m", "*",
       "-p", "Mode=tcp",
       "-p", "Host=127.0.0.1",
       "-p", "Port=1514",
       "-p", "Retry_Limit=False",
       "-p", "Syslog_Format=rfc3164",
       "-p", "Syslog_Maxsize=786432",
       "-p", "Syslog_Hostname_Key=container_id",
       "-p", "Syslog_Appname_Key=container_name",
       "-p", "Syslog_Procid_Key=pid",
       "-p", "Syslog_Message_Key=log"
     ]
   }
   ```
Note the additional `-e` parameters to load AWS-specific output plug-ins. These plug-ins can then be used for any additional log outputs, which may be configured either via [Fluent Bit launcher](#fluent_launch) (similarly to Alert Logic Agent Container), using the AWS FireLens `logConfiguration.options` field in the task definition, or via custom Fluent Bit configuration files (see [examples](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/firelens-taskdef.html) for details).

### <a name="fluent_launch"></a>Fluent Bit Launcher

The launcher, exposed from the Alert Logic Agent Container as `/var/alertlogic/lib/fluent-bit/fluent-launch`, is used to modify user-specified or FireLens-generated Fluent Bit configurations from the command line. You can use it to inject or override parameters in the `[INPUT]` sections of Fluent Bit configurations (for example, `Mem_Buffer_Limit` or `storage.type`). Fluent Bit command line, the Fluent Bit configuration `@INCLUDE` command,  FireLens configuration parameters, and [Amazon's own Fluent Bit launcher](https://github.com/aws/aws-for-fluent-bit/blob/mainline/use_cases/init-process-for-fluent-bit/README.md) all currently don't provide a way to modify the configuration for already defined input plug-in instances (only to add new instances). Therefore, using the Alert Logic launcher eliminates the need for you to create custom Fluent Bit images or entirely discard the FireLens-generated configuration in such cases. The recommended configuration uses the launcher to limit Fluent Bit's memory usage if any of its log outputs are blocked for extended periods of time (which could otherwise lead to unbounded memory usage, and eventually cause the task to be OOM-killed). It then speeds up retransmission after these outputs are unblocked.

The launcher accepts the following command-line arguments:

|                                 |     |
| ------------------------------- | --- |
| -r, --run-path \<path\>         | Fluent Bit executable to run (default `/fluent-bit/bin/fluent-bit`) |
| -c, --in-config \<path\>        | Original Fluent Bit configuration to read (default `/fluent-bit/etc/fluent-bit.conf`) |
| -m, --out-config \<path\>       | Modified Fluent Bit configuration to launch with (default `/var/alertlogic/lib/fluent-bit/fluent-bit.conf`) |
| -i, --input \<key\> \<value\>   | Key/value pair to add to each `[INPUT]` config section (\<key\> is overwritten if exists) |
| -f, --filter \<key\> \<value\>  | Key/value pair to add to each `[FILTER]` config section (\<key\> is overwritten if exists) |
| -o, --output \<key\> \<value\>  | Key/value pair to add to each `[OUTPUT]` config section (\<key\> is overwritten if exists) |
| -s, --service \<key\> \<value\> | Key/value pair to add to each `[SERVICE]` config section (\<key\> is overwritten if exists, a `[SERVICE]` section is created if not exists) |
| --                              | Pass any arguments following this one to Fluent Bit (in addition to `-c` `<out-config>`) |

Currently the launcher supports only classic (non-YAML) Fluent Bit configuration format, and does not process any `@INCLUDE` or parser files. It does not modify the configuration in place by default, since the FireLens-generated configuration is normally on a read-only mount.
