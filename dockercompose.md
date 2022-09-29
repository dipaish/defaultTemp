## Introduction to Docker Compose
- **Docker Compose** is a tool for running multi-container applications on Docker using the Compose file format
- It includes A YAML (Ain't Markup Language) file to configure your application’s services . 
-  Once you have created a Compose file, you can create and start your application with a single command: `docker compose up`
- With Docker Compose:
    - You can start/stop multiple services with a single command `Docker compose up` `Docker compose down`
    - You can also use docker to scale up/down services when needed
> Note: Docker Compose V2 is a major version bump release of Docker Compose. It has been completely rewritten from scratch in Golang (V1 was in Python). The installation instructions for Compose V2 differ from V1.

## Installing Docker Compose
Docker Compose is included in
[Docker Desktop](https://www.docker.com/products/docker-desktop)
for Windows and macOS.

#### Linux

You can download Docker Compose binaries from the
[release page](https://github.com/docker/compose/releases) on this repository.

## How to use Docker Compose?

Using Docker Compose is basically a three-step process:
1. Define your app's environment with a `Dockerfile` so it can be reproduced anywhere.
2. Define the services that make up your app in `docker-compose.yml` so they can be run together in an isolated environment.
3. Lastly, run `docker compose up` and Compose will start and run your entire service.

A docker compose file (docker-compose.yml) looks like this
```yaml
version:'3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    depends_on:
        - redis
  redis:
    image: redis
```
- `version:3`: It specifies the version of the docker-compose file syntax.
- `services:`It describes the services that are to be run. In the above example, we have two services **web** & **redis.**
- `web:` It is the name of the service. Docker Compose will create the container with the name we provide. 

- `build:` It specifies the location of a Dockerfile. The **.** sign reprents the same directory where docker-compose.yml file is located.

- `redis:` Redis is the name of the service that is built from the redis image. Docker Compose will create the container for the redis service.
- `ports:` It is describing how the container's port should be mapped to the host machine.

- `volumes:` We are attaching our files/directories to the containers'. 
- `image:` To specify a pre-built image that we want to use

- `depends_on:` It tells a dependency of a service to another.

## Creating your first Docker Compose file.
1. Create a directory and get into the directroy 
```
mkdir docker99 
cd docker99
```
2. Create a docker-compose.yml file 

```yml
services:
  database:
    image: mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
    ports:
      - "6034:3306"
    volumes: # it persists updates made to the database
      - dbdata:/var/lib/mysql
  phpmyadmin:
    depends_on:
      - database
    image: phpmyadmin
    ports:
      - "8000:80"
    environment:
      PMA_HOST: database
      MYSQL_ROOT_PASSWORD: password
volumes:
  dbdata:

```

3. Check the validity of the file 
```
docker-compose config
```

4. Run the docker-compose file in the detached mode

```
Docker-compose up -d
```

5. Check your images & containers and you will find new images & containers 

```
Docker images
Docker ps 
```
Get to the link in the browser: [http://localhost:8000/](http://localhost:8000/) and login with the root username and password. 

### Docker-compose Commom Commands

- Starts existing containers for a service.
`docker-compose start`

- Stops running containers without removing them.
`docker-compose stop`

- Pauses running containers of a service.
`docker-compose pause`

- Unpauses paused containers of a service.¶
`docker-compose unpause`

- Lists containers.
`docker-compose ps`

- Builds, (re)creates, starts, and attaches to containers for a service.
`docker-compose up`

- Stops containers and removes containers, networks, volumes, and images created by up.
`docker-compose down`

### Exercise 
1. Follow the guidelines available at this link and get more familiar with Docker Compose by completing the task. 
  [Get Started with Docker Compose](https://docs.docker.com/compose/gettingstarted/) 

2. Complete the task available at this link [Setting up Local WordPress Development using - Docker Compose](https://dipaish.github.io/docker/index.html) 