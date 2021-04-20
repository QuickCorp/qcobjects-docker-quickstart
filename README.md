# QCObjects Framework

QCObjects is a powerful framework that allows you to make advanced micro-services and micro-frontends using JavaScript

[More info of QCObjects] (https://qcobjects.dev)

# Docker

Docker is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. Containers are isolated from one another and bundle their own software, libraries and configuration files; they can communicate with each other through well-defined channels.

[More info of Docker] (https://www.docker.com)

# Dockerfile

A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application's services and define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.

# Quick Start Dockerfile contents for QCObjects

```dockerfile
# This line brings the basic image for QCObjects from the Docker Hub
FROM quickcorp/qcobjects:latest

# This line sets a work directory (the one where you want to place your app source)
WORKDIR /usr/src/app

# The next two lines are to set the NODE_ENV environment variable
ARG NODE_ENV
ENV NODE_ENV $NODE_ENV

# Following lines are the installation of your app dependencies into the container
COPY ./package.json /usr/src/app/
RUN npm install

# This line will copy all the content of your repo to the working path into the container
COPY . /usr/src/app

# It is recommended allocate the ports that you are gonna use in environment variables
ENV PORT_HTTP 80
ENV PORT_HTTPS 443

# Expose the ports for the HTTP and HTTPS services
EXPOSE $PORT_HTTP
EXPOSE $PORT_HTTPS

# The main command executed by the container and image
CMD [ "npm", "start" ]
```

# Quick Start docker-compose.yml file

```yaml
version: '2'
services:
  qcobjects:
    image: qcobjects_newapp:latest
    privileged: true
    build:
      context: ./
      dockerfile: Dockerfile
    volumes:
      - letsencrypt:/etc/letsencrypt/live/newapp.qcobjects.dev
    expose:
      - "80"
      - "443"
    ports:
      - "80:80"
      - "443:443"
    env_file:
      - env-file
volumes:
  letsencrypt:

```

In the above file, replace the name **qcobjects_newapp** and **newapp.qcobjects.dev** for your app image name and domain respectively.


The **env-file** will be used as follows:

In a shell script, copy the following lines and change the environment variables you want to export

```shell
echo -e "DOMAIN=$DOMAIN
CERTIFICATE_PROVIDER=$CERTIFICATE_PROVIDER
PRIVATE_KEY_PEM=$PRIVATE_KEY_PEM
PRIVATE_CERT_PEM=$PRIVATE_CERT_PEM
DEVMODE=$DEVMODE" > ./env-file
```

Execute the above code once before to build the image using docker-compose

# Download and execute docker-compose

To Download the latest version of docker-compose, go to the latest [docker-compose releases](https://github.com/docker/compose/releases) and pick a release version like this:

```shell
sudo curl -L --fail https://github.com/docker/compose/releases/download/<latest version release>/run.sh -o /usr/local/bin/docker-compose
```

In the above command, replace ```<latest version release>``` with the number of the version of your choice. For instance:

```shell
sudo curl -L --fail https://github.com/docker/compose/releases/download/1.29.1/run.sh -o /usr/local/bin/docker-compose
```

Then, give the execution permissions to the downloaded file

```shell
sudo chmod +x /usr/local/bin/docker-compose
```

# Building the image with docker-compose

Now you can build the image with **docker-compose build** command

```shell
sudo docker-compose -f docker-compose.yml build --no-cache --force-rm --pull -q qcobjects
```

# Starting the qcobjects service with docker-compose

Start the new service using **docker-compose up** command

```shell
sudo docker-compose -f docker-compose.yml up --quiet-pull -d qcobjects
```

# Remove the env-file

You can safely remove the env-file after you've started the server using **docker-compose up** command

```shell
rm -f ./env-file
```
