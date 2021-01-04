## Deploy the Alert Logic Agent Container for AWS Fargate
You can deploy the Alert Logic Agent Container in Amazon Elastic Container Service (ECS) environments that run Amazon Web Services (AWS) Fargate.

Use these instructions for environments running Amazon ECS tasks with the Fargate launch type. To deploy the Alert Logic Agent Container for Amazon ECS tasks with the EC2 launch type, see [ECS README](../ecs/README.md) instead. 

## AWS Fargate support
To protect environments that use Fargate with Amazon ECS, the required method is to deploy the Alert Logic Agent Container as a sidecar in each Fargate ECS task. With this method, the Alert Logic agent can still access the network interfaces of that task. Alert Logic collects network traffic and syslog messages from a specific task without violating the integrity of other customer environments in the AWS Fargate cluster.

For Alert Logic to fully integrate with a container environment, the Docker socket must be mounted through the volume mounting capability in Docker, which the Fargate environment does not allow. For this reason, Alert Logic can protect containers on Fargate workloads but does not discover other containers running on the host, capture traffic from their virtual network interfaces, or collect the native container logs stdout and stderr.

## Before You Begin
For the Cloud Defender platform, you need your unique registration key to deploy the Alert Logic Agent Container for AWS Fargate. For the Managed Detection and Response platform, which automates AWS deployments, the unique registration key is not necessary.

**To find your unique registration key (Cloud Defender platform only):**
1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

In addition, be sure you install the AWS command line interface (CLI), and ensure you point it to, and configure it for, the appropriate AWS account. For more information about the AWS CLI, see https://aws.amazon.com/cli/.

## Deploy the Agent Container for Fargate
Complete the following tasks to protect your AWS Fargate deployments:

1. Modify your AWS Fargate task definition.
2. Update the task definition.
3. Deploy the updated task definition.

### Modify your Fargate task definition
You must run the Alert Logic Agent Container as a sidecar to each Fargate ECS task. Append the following entry to the ```containerDefinitions``` section of your Fargate ECS task:
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
For the Cloud Defender platform only, in the task definition, update the ```value``` variable with your unique registration key. 

When you use a supported AWS deployment on the Managed Detection and Response platform, leave the  `KEY` environment variable undefined. AWS deployments with valid credentials do not require registration keys. The Alert Logic agent and back end gather cloud metadata to provision the deployment.

### Update the task definition
To register your task definition, enter the following command in the AWS CLI:
   ```
   aws ecs register-task-definition --cli-input-json file://path//to/task-definition.json
   ```

### Deploy the updated task definition
Use your preferred method to deploy the updated task definition. For example, you can enter the following command in the AWS CLI and replace the `{service_using_task_definition}` and `{task_definition_name}` variables with valid values:
   ```
   aws ecs update-service --service {service_using_task_definition} --task-definition {task_definition_name}
   ```
For reference, see https://docs.aws.amazon.com/cli/latest/reference/ecs/update-service.html. 
