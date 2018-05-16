## Deploy the Alert Logic Agent Container for ECS

## Before You Begin
To deploy the Alert Logic Agent Container for ECS, you will need your unique registration key. 

**To find your unique registration key:**

1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

In addition, be sure you install the AWS command line interface (CLI) and  ensure point it to, and configure it for, the appropriate AWS account. 

## Deploy the Alert Logic Agent Container Task Definition
To deploy the Agent Container for ECS, you must download the task definition file from this repository, and then edit the file to include your Alert Logic unique registration key.

**To deploy and edit the task definition file:**
1. Download the al-agent-ecs.json task definition file from this repository.

2. In the task definition file, update the ```value``` variable with your unique registration key.
   ```
   "environment": [
     {
       "name": "KEY",
       "value": "your_registration_key_here"
     }
   ]
   ```
3. In the AWS CLI, type the following command to register your task definition:
   ```
   aws ecs register-task-definition --cli-input-json file://path//to/task-definition/al-agent-ecs.json
   ```
   
## Create or Modify your IAM Policy
An AWS policy document defines your permissions for the container. You must log into into the AWS console to create a new, or modify an existing, IAM policy.

**To create an IAM policy:** 

1. In the AWS Console, click **IAM,** located under **Security, Identity & Compliance**.
2. From the IAM Management Console, click **Policies**, and then click **Create Policy**.
3. Click the **JSON** tab.
4. Copy and paste the following text into the JSON window:
   ```
   {
     "Version": "2012-10-17",
     "Statement": [
         {
             "Effect": "Allow",
             "Action": [
                 "ecs:RegisterContainerInstance",
                 "ecs:DeregisterContainerInstance",
                 "ecs:DiscoverPollEndpoint",
                 "ecs:Submit*",
                 "ecs:Poll",
                 "ecs:StartTask",
                 "ecs:StartTelemetrySession"
             ],
             "Resource": [
                 "*"
             ]
         }
     ]
   }
   ```
5. Click **Review policy**.
6. On the Review Policy page, type a **Policy Name** and **Description** for the policy.
7. Click **Create policy**.
   
## Create a Startup Script for Your ECS Instances
You can manually create a startup script to ensure the agent starts when the host starts. 

**To create the startup script:**
1. Copy the following command:
   ```
   #!/bin/bash
   mset -o pipefail

   cluster="MY_CLUSTER" # Enter your cluster name here

   task_def="al-agent-task"
   touch /etc/ecs/ecs.config || {
       echo "Error: it seems like we are not running on an ECS-optimized instance" >&2
       exit 2
   }
   set -ex
   echo ECS_CLUSTER=$cluster >> /etc/ecs/ecs.config
   start ecs
   yum install -y aws-cli jq
   instance_arn=$( curl -f http://localhost:51678/v1/metadata | jq -re .ContainerInstanceArn | awk -F/ '{print $NF}')
   az=$(curl -f http://169.254.169.254/latest/meta-data/placement/availability-zone)
   region=${az:0:${#az} - 1}
   echo "cluster=$cluster az=$az region=$region aws ecs start-task --cluster \
   $cluster --task-definition $task_def --container-instances $instance_arn --region $region" >> /etc/rc.local
   ```
2. Paste the command into the Linux command line. 

## Add Startup to ECS Cluster Instances using CloudFormation 
If you use CloudFormation, you can copy and paste the following into a template to ensure the agent starts when the host starts. 

   ```
   "UserData": {
      "Fn::Base64": {
        "Fn::Join": [
          "",
          [
            "#!/bin/bash -xe\n",
            "echo ECS_CLUSTER=",
            {
              "Ref": "YourECSClusterName"
            },
            " >> /etc/ecs/ecs.config\n",
            "start ecs || true\n",
            "sleep 30\n",
            "instance_arn=$(curl -s http://localhost:51678/v1/metadata | jq -r '. | .ContainerInstanceArn' | awk -F/ '{print $NF}' )\n",
            "az=$(curl -s http://169.254.169.254/latest/meta-data/placement/availability-zone)\n",
            "cluster=\"", { "Ref": "YourECSClusterName" }, "\"\n",
            "region=${az:0:${#az} - 1}\n",
            "task_def=\"al-agent-task\"\n",
            "echo \"\n",
            "aws ecs start-task --cluster $cluster --task-definition $task_def --container-instances $instance_arn --region $region >> /etc/rc.local\n"
          ]
        ]
      }
   }
   ```
