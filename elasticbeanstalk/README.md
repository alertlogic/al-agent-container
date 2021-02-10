## Deploy the Alert Logic Agent Container for AWS Elastic Beanstalk Multicontainer Environments

## Before You Begin
To deploy the Alert Logic Agent Container for AWS Elastic Beanstalk, you need your unique registration key unless the deployment is set up for automatic provisioning.

**To find your unique registration key (Cloud Defender platform):**
1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

## Alert Logic Agent Container Definition
**To deploy the Agent Container for Elastic Beanstalk:**
1. Download the example container definition file from this repository and merge it with your own Elastic Beanstalk container configuration file (`Dockerrun.aws.json`). The Agent Container runs as a sidecar container with your application container.
2. Include the example `.ebextensions` configuration file and merge it with your own Elastic Beanstalk application source bundle.

**To merge and edit the container definition file:**
1. Download the `Dockerrun.aws.json` [task definition file](Dockerrun.aws.json).

2. In the task definition file, update the `value` variable with your unique registration key. Note that AWS deployments with valid credentials do not require registration keys, as provisioning is performed based on cloud metadata gathered by the agent and the Alert Logic back end. When using a supported cloud deployment, the `KEY` environment variable should be undefined.
   ```
   "environment": [
     {
       "name": "KEY",
       "value": "your_registration_key_here"
     }
   ]
   ```
3. Merge the content with your existing `Dockerrun.aws.json` file from the application source bundle you plan to deploy in Elastic Beanstalk.

For more information about `Dockerrun.aws.json` formatting, see the [AWS documentation](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_v2config.html#create_deploy_docker_v2config_dockerrun)

**To merge the ebextensions file:**
1. Download the [`99-al-agent-syslog.config` file](.ebextensions/99-al-agent-syslog.config).

2. Save the file to the `.ebextensions` directory from the application source bundle packages you plan to deploy in Elastic Beanstalk.

For more information about `ebextensions` formatting, see the [AWS documentation](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html).
