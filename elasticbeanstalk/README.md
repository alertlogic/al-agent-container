## Deploy the Alert Logic Agent Container for Elastic Beanstalk (EB) Multi Containers

## Before You Begin
To deploy the Alert Logic Agent Container for EB, you need your unique registration key.

**To find your unique registration key:**

1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

## Alert Logic Agent Container Definition
To deploy the Agent Container for EB, you must:
1. Include the example container definition file from this repository and merge it to your own EB container configuration file (`Dockerrun.aws.json`). The Agent Container will run as side-car container along with your application container.
2. Include the example `.ebExtensions` configuration and merge it to your own EB application source bundle.

**To merge and edit the container definition file:**
1. Download the `Dockerrun.aws.json` task definition file from this [here](/Dockerrun.aws.json).

2. In the task definition file, update the `value` variable with your unique registration key.
   ```
   "environment": [
     {
       "name": "KEY",
       "value": "your_registration_key_here"
     }
   ]
   ```
3. Merge the content to your existing `Dockerrun.aws.json` from the application source bundle that you plan to deploy in EB

For more info about `Dockerrun.aws.json` formatting, please refer to AWS documentation [here](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/create_deploy_docker_v2config.html#create_deploy_docker_v2config_dockerrun)

**To merge the ebExtensions file:**
1. Download the `99-al-agent-syslog.config` file from [here](.ebExtensions/99-al-agent-syslog.config)

2. Save the file to `.ebextensions` directory from the application source bundle packages that you plan to deploy in EB

For more info about `ebextensions` formatting, please refer to AWS documentation [here](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/ebextensions.html)
