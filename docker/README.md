# Deploy the Alert Logic Agent Container for Docker

## Before You Begin
To deploy the Alert Logic Agent Container for Docker, you need your unique registration key. To find your unique registration key:

1. In the Alert Logic console, click the Support Information icon.
2. Click "Details."
3. Copy your unique registration key.

## Deploy the Agent Container
Use the following procedure to deploy the Agent Container to a single Docker host.

1. Copy the command below, and then paste it into the Docker command line.
	```
	docker run \
	  --name=al-agent-container \
	  --label "app=al-agent-container" \
	  --net=bridge \
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
2. Replace the value for the environment variable `KEY` with your unique registration key.
3. Modify the value for `--cpus` as necessary. <br/>
**Note:** Alert Logic recommends you limit CPU to a minimum of 1 and a maximum of 3. <br/>
	If you use Docker version 1.12 or older, use `--cpu-period="100000"` and `--cpu-quota="300000"`.
4. Press "Enter."

## Update Your Local Repository
	Alert Logic frequently updates the Docker image. To be sure your local repository is always up to date, run `docker pull alertlogic/al-agent-container:latest` regularly.
