## Alert Logic al-agent-container Deployment for ECS

#### Deploy Alert Logic Agent Task Definition

1. Update the al-agent-ecs.json with your unique registration key.
   ```
   "environment": [
     {
       "name": "KEY",
       "value": "your_registration_key_here"
     }
   ]
   ```
2. Register your task definition:
   ```
   aws ecs register-task-definition --cli-input-json file://path//to/task-definition/al-agent-ecs.json
   ```
   
#### Create or Modify your IAM Policy

1. Using the Identity and Access Management (IAM) console, create a new role called alertlogic-agent-agent-ecs.
2. Select Amazon EC2 Role for EC2 Container Service. On the next screen, do not check any checkboxes, and then click Next Step.
3. Click Create Role.
4. Click on the newly created role.
5. Expand the Inline Policies section. Click the link to create a new inline policy.
6. Choose Custom Policy and press the button.
7. For Policy Name enter alertlogic-agent-policy. Copy the following text into the Policy Document:
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
   8. Click Create Policy
   
   
   
#### Create a startup script for your ECS Instances

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

#### Add startup to ECS Cluster instances UserData

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
