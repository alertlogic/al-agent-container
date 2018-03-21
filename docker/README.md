# Docker al-agent-container Deployment

Deploying the al-agent-container for IDS on a single Docker host.

1. Use the reference below and modify it as required.
	```
	docker run \
	  --name=al-agent-container \
	  --label "app=al-agent-container" \
	  --net=bridge \
	  --cap-add=SYS_ADMIN \
		--cap-add=NET_ADMIN \
  	--cap-add=NET_BIND_SERVICE \
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
2. Update the environment variable _KEY_ with your unique registration key.
3. Default recommendation is to limit CPU to min of 1 and max of 3. Modify the flag for `--cpus` as necessary.
	If you are running Docker v1.12 or older, use `--cpu-period="100000"` and `--cpu-quota="300000"`

	## Update You Local Repository
	We frequently update the docker image, please make sure your local repository always up to date by running `docker pull alertlogic/al-agent-container:latest`
