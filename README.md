# DevOps tools
In this repositorium, I'm trying to learn tools for DevOps and understand their using purpose.

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

Commands for **system stuff**:

``docker system``
- df - for showing docker disk usage
- events - refresh actual events
- prune -a - remove images 
