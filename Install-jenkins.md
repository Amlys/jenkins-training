# The officiel doc 
    => https://www.jenkins.io/doc/book/installing/docker/

Installation steps with Windows in my case.


**Docker dind for Windows**

The Jenkins project provides a Linux container image, not a Windows container image. Be sure that your Docker for Windows installation is configured to run Linux Containers rather than Windows Containers. Refer to the Docker documentation for instructions to switch to Linux containers. Once configured to run Linux Containers, the steps are:

Open up a command prompt window and similar to the macOS and Linux instructions above do the following:
 

Create a bridge network in Docker
  
`docker network create jenkins`

Run a docker:dind Docker image

`docker run --name jenkins-docker --rm --detach --privileged --network jenkins --network-alias docker --env DOCKER_TLS_CERTDIR=/certs --volume jenkins-docker-certs:/certs/client --volume jenkins-data:/var/jenkins_home --publish 2376:2376 docker:dind`


Customize the official Jenkins Docker image, by executing the following two steps:


Create a Dockerfile with the following content:


`FROM jenkins/jenkins:2.426.3-jdk17

USER root

RUN apt-get update && apt-get install -y lsb-release

RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \

  https://download.docker.com/linux/debian/gpg

RUN echo "deb [arch=$(dpkg --print-architecture) \

  signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \

  https://download.docker.com/linux/debian \

  $(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list

RUN apt-get update && apt-get install -y docker-ce-cli

USER jenkins

RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"`


Build a new docker image from this Dockerfile and assign the image a meaningful name, e.g. "*amlys-jenkins*":

`docker build -t amlys-jenkins .`

If you have not yet downloaded the official Jenkins Docker image, the above process automatically downloads it for you.

Run your own amlys-jenkins image as a container in Docker using the following docker run command:


`docker run --name jenkins-blueocean --restart=on-failure --detach --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro --publish 8080:8080 --publish 50000:50000 amlys-jenkins`

docker run --name jenkins-blueocean --restart=on-failure --detach --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro --publish 8080:8080 --publish 50000:50000 amlys-blueocean:2.426.3-1
Proceed to the Setup wizard.