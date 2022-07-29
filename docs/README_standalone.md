# Standalone topology

> :warning: **This dev branch contains instructions to build a test image for arm64 architecture**: It's not supported at all and is only there waiting for an official amr64 port of WLP!

This tutorial explains how to build and start a docker image that includes all the IBM Operational Decision Manager components in one container.

![Flow](images/StandaloneFig01.png)

## Setting up your environment

Before you proceed, install [Docker and Docker Compose](https://docs.docker.com/compose/#installation-and-set-up).

## Create an Liberty arm64 image

There is currently no official Liberty image (with JDK8) built for arm64.

Tim Raasveld explained [here](https://github.com/OpenLiberty/ci.docker/issues/241) how to build an OpenLiberty with OpenJDK 8 image for such architecture:

```console
git clone git@github.com:OpenLiberty/ci.docker.git
cd ci.docker/build
./build.sh --dir=../releases/latest/full --dockerfile=Dockerfile.ubuntu.openjdk8 --tag=open-liberty:full
```

### Install Operational Decision Manager

To create the Operational Decision Manager docker image, install one of the following components:    
* Decision Center, with the WebSphere Liberty Profile option,
* Decision Server Rules, with the WebSphere Liberty Profile option.

Go to the Operational Decision Manager installation directory and locate the required WAR files in the directories listed below:

_installation_directory/executionserver/applicationservers/WLP*/res.war_

_installation_directory/executionserver/applicationservers/WLP*/DecisionService.war_

_installation_directory/teamserver/applicationservers/WLP*/decisioncenter.war_

_installation_directory/teamserver/applicationservers/WLP*/decisionmodel.war_ (starting from ODM vnext)

_installation_directory/executionserver/applicationservers/WLP*/DecisionRunner.war_

### Clone the odm-ondocker code

From the ODM installation directory, enter ```git clone git@github.com:PYLochou/odm-ondocker.git --branch build-arm64```.

### Copy .dockerignore file

Copy the odm-ondocker/resources/.dockerignore file into the ODM installation directory.

```cp odm-ondocker/resources/.dockerignore ./```

When the copy is complete, the content of your repository should be similar to this:

![Flow](images/Fig2.png)

### Verify that Docker Engine is running

Open a command prompt and run the following command:    	

  ```
    > docker -–version
    Docker version 1.12.3
  ```

Now you are ready to build and run the docker images.

## Building and running the docker image
Open a command prompt in the directory **installation_directory/odm-ondocker** and run the following command:    	

```
docker-compose -f odm-standalone.yml up
```

Note: If you have trouble on Windows os see this [issue](https://github.com/ODMDev/odm-ondocker/issues/100)

This command creates one docker container with the following components:

* Embedded Derby database
* HA Proxy load balancer
* ODM Decision Server runtime
* ODM Decision Server console
* ODM Decision Center
* ODM Decision Runner

You can access the application with these URLs:

|Component|URL|Username|Password|
|:-----:|:-----:|:-----:|:-----:|
| [Decision Server Console](http://localhost:9080/res) | <http://localhost:9080/res> |resAdmin|resAdmin|
| [Decision Server Runtime](http://localhost:9080/DecisionService) |<http://localhost:9080/DecisionService> |N/A|N/A|
| [Decision Center Business Console]( http://localhost:9080/decisioncenter) |  <http://localhost:9080/decisioncenter> |rtsAdmin|rtsAdmin|
| [Decision Runner]( http://localhost:9080/DecisionRunner) |  <http://localhost:9080/DecisionRunner> |resDeployer|resDeployer|

## Verifying the docker images

You can check the container status with the following command:
```
 docker-compose ps
```
