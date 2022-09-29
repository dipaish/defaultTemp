# Interacting with Docker Shell 
- Lets first create a Docker instance using an Alpine image. 
- To attach a shell, run the following command 
```
PS D:\github\OS22> docker run -it alpine /bin/sh
/ #
```
- To interact with the shell when the container is already running. 
```
PS D:\github\OS22> docker exec -it d991 /bin/sh
/ #
```
- To interact with the shell with Docker Desktop
![Accessing Shell using Docker Desktop](assets/accShell.png)

## Managing Docker Containers

Lets run the following containers: 

`docker run --name app1 -d -p 88:80 dockersamples/static-site`

`docker run --name app2 -d -p 89:80 dockersamples/static-site`

Visit the following links: 
 - app1: [http://localhost:88/](http://localhost:88/)
 - app2: [http://localhost:89/](http://localhost:89/)

***To get a list of running containers : `docker ps`***
```
PS D:\github\OS22> docker ps
CONTAINER ID   IMAGE                       COMMAND                  CREATED       STATUS       PORTS                                   NAMES
5df0222c409e   dockersamples/static-site   "/bin/sh -c 'cd /usr…"   2 hours ago   Up 2 hours   443/tcp, 0.0.0.0:88->80/tcp             app1
5806d0aee950   dockersamples/static-site   "/bin/sh -c 'cd /usr…"   2 hours ago   Up 2 hours   80/tcp, 443/tcp, 0.0.0.0:8888->85/tcp   app2
```
***To Stop the container (app1) : `docker stop app1`*** You can stop a container with the container ID or the name. You need to type in only the first few letters of the container id. 
```
PS D:\github\OS22> docker stop app1
app1
```
***To show all the containers (includind non running containers):***
```
$ docker ps -a
```

***To  start container(s)***
```
$ docker start app1 app2
```

***To restart the  container***
```
$ docker restart app2
```
***To display the docker host information***
```
$ docker info
```

***To show the running processes in the  container `docker top container id or the name`***
```
$ docker top app1
```

***Retrieve the history of container `docker history ImageID/repository name`***
```
docker history 9c6

```

***To Inspect the image `docker inspect imageid/name`***
```
$ docker inspect 9c6
```

***Inspect one of the container and look for the internal ip***
```
$ docker inspect app1
```


***Show the logs of the second container using the flag --follow***
```
$ docker logs --follow app2
```

***Browse to the application and see the containers logs from the terminal***
[http://localhost:88](http://localhost:88)

***To stop  tracking logs***
```
$ CTRL + C
```
# Docker and Visual Studio Code
 - **Remote - Containers** extension in Visual Studio Code lets you use a Docker container as a full-featured development environment. 
 - You can  open any folder inside (or mounted into) a container.
 - You can easily switch between your containers and have full access to the tools, platform and file system.

 ![Remote - Container Extension](assets/remote.png)
 ![Docker in VSC](assets/dockervi.png)
 
# Docker Volumes

- Volumes are special directories in a container. 
- We can use *[Docker Volumes](https://docs.docker.com/storage/volumes/)* to persist data that are generated and used by Docker containers. 
- Volumes are managed by Docker and it is preffered mechanism to persist data. 
- The another mechanism to persist data is [Bind Mounts](https://docs.docker.com/storage/bind-mounts/).

***To make things clear, lets go through a simple demonstration of a volume***

- Run an interactive shell within an alpine container 
```docker
docker run -it alpine /bin/sh
```
- Lets create a new folder as mydata
    ```bash
        mkdir mydata
    ```
- Lets create a text file as hello.txt in 'mydata'
```bash
touch mydata/hello.txt
```
- Lets confirm the file is created. 
```bash
ls mydata
```
- Lets exit the container shell 
```docker
exit
```
 ***The container has now exited. Let's start the container again.***
```docker
docker start continerID
```
#### Let's access the shell and see what happens to the file we created earlier, the file is still there 
```bash
PS D:\github\OS22> docker exec -it 661 /bin/sh
/ # ls
bin     etc     lib     mnt     opt     root    sbin    sys     usr
dev     home    media   mydata  proc    run     srv     tmp     var
/ # ls mydata/
hello.txt
/ #
```
#### To access the file from the host machine in Windows 10/11 (Make sure that you have WSL integration checked as in the diagram below:)
 ![Docker in VSC](assets/wsl.png)


Let’s inspect our container in order to get the location of the container’s layer. We can use the `inspect` command and then scroll into the output until the GraphDriver key.

```docker
docker container inspect yourcontainerid
```
Get the path of the file that is specified in *UpperDir* and copy the path
 ![Check path for the upperDir](assets/upperD.png)

#### To access the directory or file from the Windows machine, type the command below: (please remember to replace the path with your own path)
```powershell 
PS D:\GitHubRepos\OS22> ls \\wsl.localhost\docker-desktop-data\data\docker\overlay2\c9cd2164b2f7f3c1b27d4729631b73ea2fc52137a3be379edf1b054201676a1b\diff\mydata

    Directory: \\wsl.localhost\docker-desktop-data\data\docker\overlay2\c9cd2164b2f7f3c1b27d4729631b73ea2fc52137a3be379edf1b054201676a1b\diff\mydata

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-----          13/09/2022     8.59              0 hello.txt
```
#### You can aslo access it from Windows file explorer. 

[![Click to Watch Video](assets/www.png)](https://www.youtube.com/watch?v=-JKWxSPdwD4 "Click to Watch Video")

#### Now let's remove the container & try to access the path
```docker
PS D:\GitHubRepos\OS22> docker stop  661
661
PS D:\GitHubRepos\OS22> 
PS D:\GitHubRepos\OS22> docker rm 661
661
PS D:\GitHubRepos\OS22> ls \\wsl.localhost\docker-desktop-data\data\docker\overlay2\c9cd2164b2f7f3c1b27d4729631b73ea2fc52137a3be379edf1b054201676a1b\diff\mydata
Get-ChildItem: Cannot find path '\\wsl.localhost\docker-desktop-data\data\docker\overlay2\c9cd2164b2f7f3c1b27d4729631b73ea2fc52137a3be379edf1b054201676a1b\diff\mydata' because it does not exist.
PS D:\GitHubRepos\OS22> 

```
***It seems the folder defined in the UpperDir above does not exist anymore.  Try running the ls command again and see the results.***

>Note: The data created in a container is not persisted. It’s removed with the container’s layer when the container is deleted.To persis data, we will use volume.

# Creating a Docker Volume
- You can create Docker Volume in two different ways
    - Within a `Dockerfile` 
    ```
    VOLUME /mydata
    ```
    - With the Docker run command with the `-v` flag
    ```bash
    $ docker run -d -v /mydata myapp
    ```
##### In both cases, `/mydata` (inside the container) will be a volume.

***You can create and manage volumes that are outside the scope of any container***
```docker
docker volume create myvolume
```
### To list volumes
```docker
docker volume ls
```
### To inspect a volume 
```docker
docker volume inspect volumename
```
### To remove a volume 
```docker
docker volume rm volumename
```
### To remove (pruning) all unused volumes 
```docker
docker volume prune
```
### To start a container with a volume
When you start a container with a volume that does not exist, the Docker will create the volume for you. 
***Lets start alpine with a volume to which we will add persistent data.***

- Create a volume or use the existing one. Lets create a new volume called alpinedata
```docker
docker volume create alpinedata
```
- Create a Docker container & attach the alpinedata volume
```docker
docker run -it -v alpinedata:/data alpine /bin/sh
```
- Lets get to the data directory in container and create some files and ensure with ls command that the files are created.
```bash
touch data/file1.txt data/file2.txt
ls data
```
- Exit the container `exit`
- Delete the container `docker rm continaerid`

### Run a new container and attach the alpinedata volume to it. 
```bash
docker run -it -v alpinedata:/data alpine /bin/sh
```
#### [Read More on Volumes](https://docs.docker.com/storage/volumes/)

#### Check the files in the data directory and verify that they still exist 
```bash
ls data
```
## Creating Docker Volume within a **Dockerfile**

###### Content of the Dockerfile
```bash
FROM alpine:latest
RUN mkdir /data
WORKDIR /data
RUN echo "Hello from Volume" > test
VOLUME /data
CMD cat test
```
- Build the image `docker build . -t   vv`
- Run it `docker run vv`  
- Get the shell access and create some more files to it.
- Delete the container and run the image again by attaching the volume . 
- Check if your files still exist.