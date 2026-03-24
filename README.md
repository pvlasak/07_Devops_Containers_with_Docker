# 07_Devops_Containers_with_Docker

*docker run -d \
> -p 27017:27017 \
> --network mongo-network \
> -e MONGO_INITDB_ROOT_USERNAME=admin \
> -e MONGO_INITDB_ROOT_PASSWORD=password \
> --name mongodb \
> mongo*

*docker run -d \
> -p 8081:8081 \
> --network mongo-network \
> -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
> -e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
> -e ME_CONFIG_BASICAUTH_USERNAME=user \
> -e ME_CONFIG_BASICAUTH_PASSWORD=pass \
> -e ME_CONFIG_MONGODB_URL="mongodb://mongodb:27017" \
> -e ME_CONFIG_MONGODB_SERVER=mongodb \
> mongo-express*

To automate starting multiple containers we can use **docker compose file**:
- it is not necessary to create a network, docker compose will creates default network where containers are running. 
*docker compose -f <yamlfile> up*
Mongo Express has to wait until MongoDB is started - it allows command *restart: always*

**To build docker image** a Dockerfile must be created before. A docker image can be started by running the command: 
*docker build -t my-app:1.0 <location_where_to_build> *
Dockerfile usually contains following commands:
- FROM <image> - name of the image that is a base for the new image
- ENV - environmental variables 
- RUN - commands executed inside a container
- COPY - command executed on a host
- WORKDIR - following commands in Dockerfile will be executed in this directory
- CMD - entrypoint 

**To access private repository** a user, role, blob store and remote repository itself must be created in Nexus UI. 
Docker client cannot connect to URL, instead a port has to be released for connection, the port number is different from port where the nexus is running (e.g.8083). This port must be also allowed in firewall configuration. It can be checked by *ss -tulpn* or by *nestat-lnpt*, that the port is listening.  
Before the first login to nexus repository, the issuing of the login token must be set in Realms in Nexus UI - `Docker Bearer Token` has to be active.
In case the unsecured HTTP protocol is used to connect to nexus repository, a file `/etc/docker/daemon.json` must be created and insecured registry has to be added:
*{
  "insecure-registries": ["ip_address:port"]
}*
After the first login the release token has been added to `~/.docker/config.json`

Rename the image to include also target repository name: 
*docker tag <original_image_name> <docker_repository_IP:port/new_image_name>*

To fetch data from repository a **curl API** command using **port 8081** can be used

To deploy an application on development server, not on the localhost, the API endpoints and mongodb URL must be adjusted. 

### Docker Volumes
1. *docker run -v host_volume:container_directory* - **host volume**
2. *docker run -v container_directory* - **anonymous volume**
3. *docker run -v name:container_directory* - **named volume**

MongoDB saves data inside `/data/db` directory inside the container. This directory can be linked to the name of volume and defined in a docker compose file. 
On Linux localhost or Linux dev server the volume data are saved on `/var/lib/docker/volumes`.

Starting **Nexus as docker container**, the volume can be created to persists the data. 
volumes can be checked by: *docker volume ls*
info about the mountpoint of the volume:*docker inspect nexus-data*
Docker container is automatically running as non-root user called `nexus`.
