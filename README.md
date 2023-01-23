# Docker in Docker 
There maybe many situations in which you will need to run docker-compose inside of another container, generally for CICD purposes (say for example a stupid bitbucket pipeline that does not support docker-compose which was released half a decade ago). There are a few ways to acheive that: 
- Either use a dind image (docker in docker) which has both docker client and daemon installed.
- But a faster, lighter and cacheable option is to simply install docker cli like docker-compose inside of the docker container, and map the docker.sock file inside the container to the host machines docker.sock file, so the container can pretty much run docker using the hosts docker engine. 
	- Here 's a blogpost explaining why its superior, by the guy who made the `dind` image [Using Docker-in-Docker for your CI or testing environment? Think twice](http://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/)
- The command required to do the above [varies from platform to platform](https://stackoverflow.com/a/62176649/13400850)
-  on windows follow this command:
```bash
docker run -it --entrypoint sh -v "//var/run/docker.sock://var/run/docker.sock" image_with_docker
```
- now there are many ways to install docker-compose inside of an alpine image. 2 most popular ones i found are:
1. [Use](https://bitbucket.org/magnet-coop/bitbucket-pipelines-docker-compose/src/master/ci/dependencies.sh) [pip](https://pypi.org/project/docker-compose/) - only extends till v1.29.2 
2. [Use curl](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-22-04) - works with latest releases (check code below)

```dockerfile
RUN apk update && \
    set -eu && \
    apk add --no-cache docker-cli curl && \
    mkdir -p ~/.docker/cli-plugins/ && \
    curl -SL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64 -o ~/.docker/cli-plugins/docker-compose && \
    chmod +x ~/.docker/cli-plugins/docker-compose && \
    docker compose version
```
To pull the image directly from docker hub:

```bash
docker pull xidl/alpine-compose
```
