## Deploy the Alert Logic Agent Container for AWS Fargate
You can deploy the Alert Logic Agent Container in Amazon Elastic Container Service (ECS) environments that run Amazon Web Services (AWS) Fargate.

Use these instructions for environments running Amazon ECS tasks with the Fargate launch type. To deploy the Alert Logic Agent Container for Amazon ECS tasks with the EC2 launch type, see [ECS README](../ecs/README.md) instead. 

## AWS Fargate Support
To protect environments that use Fargate with Amazon ECS, the required method is to deploy the Alert Logic Agent Container as a sidecar in each Fargate ECS task. With this method, the Alert Logic agent can still access the network interfaces of that task. Alert Logic collects network traffic and syslog messages from a specific task without violating the integrity of other customer environments in the AWS Fargate cluster.

For Alert Logic to fully integrate with a container environment, the Docker socket must be mounted through the volume mounting capability in Docker, which the Fargate environment does not allow. For this reason, Alert Logic can protect containers on Fargate workloads but does not discover other containers running on the host, capture traffic from their virtual network interfaces, or collect the native container logs stdout and stderr.

## Agent Container for Fargate for Managed Detection and Response Customers

### Deploy Agent Container 

Complete the following steps to protect your AWS Fargate deployments:

1. Modify your AWS Fargate task definition.

You must run the Alert Logic Agent Container as a sidecar to each Fargate ECS task. Using the JSON tab, append the following container definition to the ```containerDefinitions``` array within your existing Fargate ECS task definition:
   ```
   {
     "name": "al-agent",
     "image": "alertlogic/al-agent-container:latest",
   }
   ```
Alternatively, another option is to 'Create a New Revision' using the Builder and 'Add Container' with the above image.

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

You must run the Alert Logic Agent Container as a sidecar to each Fargate ECS task. Using the JSON tab, append the following container definition to the ```containerDefinitions``` array within your existing Fargate ECS task definition:
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
   }
   ```
In the task definition, update the ```value``` of the ```KEY``` environment variable with your unique registration key. 

Alternatively, another option is to 'Create a New Revision' using the Builder and 'Add Container' with the above image. You must then add the environment variable ```KEY=<your_registration_key_here>```.

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
