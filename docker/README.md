# Deploy the Alert Logic Agent Container for Docker

## Before You Begin
To deploy the Alert Logic Agent Container for Docker, you need your unique registration key unless the deployment is set up for automatic provisioning.

**To find your unique registration key (MDR platform -- Data Center deployments only):**
1. In the Alert Logic console, navigate to Configure > Deployments section.
2. Click on the Data Center deployment.
3. Click Configuration Overview > Installation Instructions.
4. Copy your unique registration key for the appropriate network. The network must have a subnet with an IP range matching the agent's base host IP address(es), otherwise agent provisioning will fail.

**To find your unique registration key (Cloud Defender platform):**
1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

## Deploy the Agent Container Using the Standard Docker "Run" Command
Use the following procedure to deploy the Agent Container to a single Docker host.

1. Copy the command below, and then paste it into the Docker command line.
	```
	docker run \
	  --name=al-agent-container \
	  --label "app=al-agent-container" \
	  --net=host \
	  --privileged \
	  --mount type=bind,source=/var/run/docker.sock,target=/var/run/docker.sock \
	  --mount type=bind,source=/proc,target=/host/proc \
	  --memory-reservation=100m \
	  --memory=500m \
	  --cpus=3 \
	  --restart=always \
	  -d=true \
	  -e "KEY=your_registration_key_here" \
	  alertlogic/al-agent-container:latest
	```
2. Replace the value for the environment variable `KEY` with your unique registration key. Note that supported cloud deployments with valid credentials do not require registration keys, as provisioning is performed based on cloud metadata gathered by the agent and the Alert Logic back end. When using a supported cloud deployment, the `KEY` environment variable should be undefined.
3. Modify the value for `--cpus` as necessary. <br/>
**Note:** Alert Logic recommends you limit CPU to a minimum of 1 and a maximum of 3. <br/>
	If you use Docker version 1.12 or older, use `--cpu-period="100000"` and `--cpu-quota="300000"`.
4. Press "Enter."

## Deploy the Agent Container in Docker Swarm
The al-agent-container requires --privileged mode in order to gain escalated access to analyze packets at the network level.  At this time, Docker Swarm does not support creating a service in "Privileged Mode".  There is an outstanding request to add this capability into Docker Swarm; however, until the open issue is resolved the recommended method is to run the al-agent-container as a standalone container on any Docker host participating in the swarm.  The al-agent-container will operate properly and will analyze the network traffic to/from the host it is deployed on; as well as, the container-to-container (or service-to-service) traffic.

## Update Your Local Repository
	Alert Logic frequently updates the Docker image. To be sure your local repository is always up to date, run `docker pull alertlogic/al-agent-container:latest` regularly.
