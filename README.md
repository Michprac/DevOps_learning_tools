# DevOps tools
In this repositorium, I'm trying to learn tools for DevOps and understand their using purpose.

# Linux on Windows

If a user works on the Windows OS, good choice of studying Linux can be thing called WSL ([Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/install)). If I understood correctly, installing WSL can provide you Linux subenvironment on your Windows system with a proper File System. Advantages:

- Helpful during Docker installation on Windows, because Docker is based on Linux

- Useful for Linux learning using terminal. Users shouldn't install additional programs like VirtualBox

# Docker

We use Docker for running some applications in containers. Users shouldn't prepare their own environment (downloading software, configure it, etc), but they can just download prepared image with requiered settings.

Docker is lightweight solution. Before it slow virtual machines were used. In Docker you should install Docker engine and containers will be deployed using resources from it. Containers are isolated from eaach other.

When the application stopped the container is shut down respectively.

Image - it is repositorium in the Docker Hub with specific settings. Container - it is "child from an image", from one image can be produced many containers. The next diagram can illustrate this relationship:

```mermaid
flowchart TD
  A[Docker Hub] --> B[Repositorium]
  B --> B1[Image]
  B1 --> C[Container 0.0.1]
  B1 --> D[Container 0.0.2]
  B1 --> E[Container 0.0.3]
```

A good feature of the Docker is optimalization of the memmory use. If you want to build different versions of the image, Docker look through a cash to use already completed and not changed levels. Such process is called **layer caching**.

## Useful commands
Explanation of some commands:
- ``docker run [OPTION] [NAME]:[TAG]``  this command used for running container. If the image wasn't find locally it would be pulled from Docker Hub
  - **NAME** - name of the repositorium in the Docker Hub
  - **TAG** - version of the repositorium
  - **OPTION** - options of command `run`:
    - -p a:b (--publish a:b) - mapping ports, where a - host port and b - internal port of the container
    - -d (--detached) - helps to run container in the background mode
    - -m 512m (--memory) - uses specific amount of memory for the container, in this example it is 512 MB
    - -cpu-quota=50000 - gives limit for cpu power, in this example it is 50% of cpu power (max = 100 000)
- ``docker logs [OPTiON] [ID]`` this command shows logs info of the container
  - **ID** - ID number of the container
  - **OPTION** - options of command `logs`:
    - -f (--follow) - enter logs process and refresh actual logs
- ``docker ps [OPTiON]`` this command shows containers that are actually running
  - **OPTION** - options of command `logs`:
    - -a (--all) - shows all containers as well as running and stopped

Commands for **images**:

``docker image``
 - -inspect [ID] - shows image info
 - -history [ID] - shows all layers of the image
 - -remove [ID/NAME] - remove image

Commands for **cotainers**:

``docker container``
- -inspect [ID] - information about container (IP, port, image name, etc)
- -un/pause [ID] - unpause or pause a container
- -stop [ID] - stop a container with some time for ending some processes
- -kill [ID] - stop a container immediately without additional time for ending processes of the container
- -prune - deletes all inactive containers

Commands for **system stuff** and other:

``docker system``
- df - for showing docker disk usage
- events - refresh actual events
- prune -a - remove images

``docker network``
- ls - shows networks in the docker, e.g. bridge
- inspect [NAME] - shows detailed information about network, where NAME can be e.g. bridge
## Creating image

Example form for building image:
- FROM python:alpine - base laer of the image. The next layers  will be overlap on this one
- WORKDIR /app - creating and NAVIGATING to the application folder /app. So application files will be in one place
- COPY . /app - copying files from your current path (.) to the destination (/app)
- RUN pip install -r requirements.txt - running command in command prompt (in this example installing python package from the requirements file)
- EXPOSE 1234 - exposing port 1234
- CMD python ./main.py - startng application running python file launch.py

!!! Also instead of using ``CMD python ./main.py`` you can use command ``ENTRYPOINT ["python", "/main.py"]``. The differenc of these commands is that the first one can be replaced bu putting any command after ``run`` (like ping site.com). But for the second command, you should use option ``--entrypoint [COMMAND]`` for replacement. The second variant is more prefered. 

To build image, go to the folder witth Dockerfile and run the next command:

``docker build -t [NAME]:{VERSION} .`` - where name and version mean name of the image and version respectively

To push your created image on the Docker Hub you can  run the command:

``docker push [NAME]:[VERSION]``

## Microservices

Microservice - it is small applicatio. Instead of deploying one monolit application user can split application with multiple microservice. The famous technology that can be introduced here called *Microservices chain*:

```mermaid
graph LR
A[Miroservice 1] --> B[Microservice 2]
B --> C[Microservice 3]
C --> D[Microservice 4]
```

So you can imagine microservice simply like separete containers. By default they are not connected.

The first option of establish communication between containers is called bridge network. When you have 2 containers in the bridge network, you can use option ``--link [NAME_CONTAINER_1]`` when creating the second container. Also you need to specify global variable of the address on which your first cntainer can be availale, it looks like ``--env GLOBAL_VAR=*http://NAME_CONTAINER_1*``.

The second option called host network. But this option is not suitable if the user works on Mac or Windows.

And the third option is creating custom network. For this purpose you can use command ``docker network create [NAME]``. The next step is to create two container with a special option ``--netwok=[NAME]``, which means adding containers to this custom network. Global variable remains the same as it was in the first variant.

## Volumes

Allows to mapp your local host folder to the folder in a container. 

``docker run --name abc --rm -v C:\your\project\path:/app -p 40:345 image_app:v1`` - mapps project directory **C:\your\project\path** on the local host to the directory **/app** in the container

## Docker Compose

Is used in the situations when we have multiple containers and we need them communicate to each other. SO in simple words, all instructions that were written in the command prompt will be written in the one file *docker-compose.yml*.
The structure and dependednses of microservice in the .yml file you can see in the image below:

<p align="center">
  <img src="/docker_img/microservices_yml.png" width=500 align:middle>
</p>
To run this file you can use a command ``docker-compose up (-d for the detached)``.

To stop and remove containers with network you can use command ``docker-compose down``.

Commands:
- ``docker-compose events`` - actual events
- ``docker-compose config`` - docker-compose.yml file architecture, helps to find type-mistake.
- ``docker-compose images`` - shows images
- ``docker-compose top`` - shows top processes, that are running in each container.
- ``docker-compose stop, kill, un/pause`` - the same as for ``docker container``

# Kubernetes

This tool helps to manage a big amount of containers. It is declarative, so we can describe the result we want in the YAML file.

## Useful commands

Kubectl stands for Kubernetes control. Structure of Kubernetes can be described with pods and nodes. You can imagine pod as a container for our containers. Pod has a uniqe IP address, each pod can have multiple containers. You can give specific name dor your pod to describe containers inside, e.g. Development or Production. Node contains pods, it is a host. On the image below, you can see structure:

<p align="center">
  <img src="/kubernetes_img/pods_nodes.png" align:middle>
</p>

Replicasets insures that specific number of pods are running.

- ``kubectl create deploument [DEPLOYMENT_NAME] --image=[IMAGE NAME]:[TAG]`` - deployment of the kubernetes cluster
- ``kubectl expose deploument [DEPLOYMENT_NAME] --type=LoadBalancer --port=1234`` - exposing cluster for demonstrating on the port 1234
- ``kubectl scale deployment [DEPLOYMENT_NAME] --replicas=3`` - increasing number of pods to 3
- ``kubectl set image deployment [DEPLOYMENT_NAME] [DEPLOYMENT_NAME]=[IMAGE_NMAE]:[TAG]`` - setting new version of the application to the deployment
- ``kubectl apply -f [FILE.YAML]``
- ``kubectl delete all -l app=hello-world-rest-api`` - deleting of deployment, pods, services

For show:

- ``kubectl get events --sort-by=.metadata.creationTimestamp`` - show all events that happened with sorting by time
- ``kubectl get pods`` - show all pods
- ``kubectl describe pod [POD_ID]`` - show description of the specific pod
- ``kubectl get replicaset/rs [-o wide]`` - show all replicasets, number of pods run at all time, where *-o wide* it is used for more info
- ``kubectl delete pods [POD_NAME]`` - delete pod
- ``kubectl get deployment`` - show all deployments
- ``kubectl get componentstatuses`` - show status of components of master node

