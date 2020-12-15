## Deploy the Alert Logic Agent Container for AWS Fargate

## Before You Begin
- To deploy the Alert Logic Agent Container for Amazon ECS, you need your unique registration key unless the deployment is set up for automatic provisioning.
- These instructions are for ECS tasks with Fargate launch type. To deploy the Alert Logic Agent Container for ECS tasks with EC2 launch type, see [ECS README](../ecs/README.md) instead.


**To find your unique registration key (Cloud Defender platform):**
1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

In addition, be sure you install the AWS command line interface (CLI), and ensure you point it to, and configure it for, the appropriate AWS account. For more information about the AWS CLI, see https://aws.amazon.com/cli/.

## Modify your Fargate Task Definition
The Alert Logic Agent Container must be run as a sidecar to each Fargate ECS task. Append the following entry to ```containerDefinitions``` section of your Fargate ECS task:
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
In the task definition, update the ```value``` variable with your unique registration key. Note that AWS deployments with valid credentials do not require registration keys, as provisioning is performed based on cloud metadata gathered by the agent and the Alert Logic back end. When using a supported cloud deployment, the `KEY` environment variable should be undefined.

## Update the Task Definition
In the Linux command line, type the following command to register your task definition:
   ```
   aws ecs register-task-definition --cli-input-json file://path//to/task-definition.json
   ```

## Deploy the updated Task Definition
Follow your preferred method of deploying the updated Task Definition, e.g. https://docs.aws.amazon.com/cli/latest/reference/ecs/update-service.html:
   ```
   aws ecs update-service --service {service_using_task_definition} --task-definition {task_definition_name}
   ```
