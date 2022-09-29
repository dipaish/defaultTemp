# 2. Docker Images

Previous, we utilized **images** from the **registry**  and asked the Docker client to run a container **based** on that image. 

To see the list of images that are available locally on your system, run the `docker images` command.

```bash
$ docker images
```
**Example**
```
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
alpine        latest    9c6f07244728   2 weeks ago     5.54MB
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB
```
Above is a list of **images** that I've pulled from the registry. You will have a different list of images on your machine. 

- The `TAG` refers to a particular snapshot of the image and the `ID` is the corresponding unique identifier for that image.

**Images** can be [committed](https://docs.docker.com/engine/reference/commandline/commit/) with changes and have multiple versions. 

>When you do not provide a specific version number, the client defaults to `latest`.

For example you could pull a specific version of `ubuntu` image as follows:

```bash
$ docker pull ubuntu:20.04
```

- If you do not specify the version number of the image then, as mentioned, the Docker client will default to a version named `latest`.

***For example to get the latest version of ubuntu image:***

```bash
$ docker pull ubuntu
```

- You can get a new Docker image  from a registry (such as the **Docker hub**) or create your own. 
- There are hundreds of thousands of images available on [Docker Store](https://store.docker.com). 
- You can also **search for images** directly from the command line using `docker search`.<br> <br>
 ***For example, if you want to search a MySQL related Image*** 
```
$ docker search mysql
NAME                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…   13077     [OK]
mariadb                         MariaDB Server is a high performing open sou…   5001      [OK]
phpmyadmin                      phpMyAdmin - A web interface for MySQL and M…   614       [OK]       
percona                         Percona Server is a fork of the MySQL relati…   584       [OK]
```   
### Types of Images

- **Base images** are images that have no parent images, usually images with an `OS` like ubuntu, alpine or debian.

- **Child images** are images that are build on base images and add additional functionality.

Another key concept is the idea of _official images_ and _user images_. (Both of which can be base images or child images.)

**Official images**
- Images that are verified by Docker.
- A dedicated team is responsible for reviewing and publishing all Official Repositories content. 
- ***Examples:*** `python`, `node`, `alpine` and `nginx` images are official (base) images. To find out more about them, check out the [Official Images Documentation](https://docs.docker.com/docker-hub/official_repos/).

**User images** 
- Images created and shared by users like you and me. 
- They build on base images and add additional functionality. 
- Typically these are formatted as `user/image-name`. The `user` value in the image name is your Dockerhub user or organization name.

### Create your first image
Now that you have a better understanding of images, it's time to create your own.

#### Docker File
- A Dockerfile is simply a text file that contains the build instructions for an image. It automates the image creation process.
  - It specifies a base image.
  - It includes instructions to install required tools for your app. 
  - It includes intructins to install libraries, dependencies and pacakages. 
  - It then builds your app. 
- It is a step by step set of standard instructions such as `FROM`, `COPY`, `RUN`, `ENV`, `EXPOSE`, `CMD` 
- The commands you write in a Dockerfile are almost identical to their equivalent Linux commands.

- The name of the file is  `Dockerfile` without any extension and the letter **D** is capital. 

- Write only the minimum set of steps that is needed for your app. Avoid unnecessary components. 

#### Dockerfile basic commands 
***FROM***
 - The **FROM** command must be the first line in the Dockerfile. Since images are made up of layers, you can utilize one of those images as the foundation for your own. The **FROM** command defines your **base layer**. 
```  
FROM alpine:3.14 
``` 
> The above line specifies that the base image is going to be alpine:3.14. 

- It accepts the image's name as parameters. You can optionally include the image version and the maintainer's Docker Hub username in the following format:`username/imagename:version`.

***WORKDIR***
- It defines the working directory of a Docker Conainter.
- Any RUN, CMD, COPY or ENTRYPOINT COMMAND will be executed in the specified working directory.

##### Lets specify our working directory in the Docker file. 

```  
FROM alpine:3.14 
WORKDIR /usr/src/app/ 
``` 
***COPY***
- It copies local files or directories into the container.

 COPY `<src>` `<destination>`

 To understant the concept, let's write a simple script called **hello.sh** in your device. We will copy this file to the container and ask to run at the time of building our image.

 ```
 #!/bin/sh
 echo "Hello, World, I am learning to write a Dockerfile!"
 ```
***Now our Docker file looks like this***
```  
FROM alpine:3.14 
WORKDIR /usr/src/app/  
COPY hello.sh .
``` 
***RUN***
- It allows you to install your applications and pacakages you need for your app. 
-  For each RUN command, Docker will run the command then create a new layer of the image. 
- The Docker daemon runs instrcutions in the Docker file one by one. 
- Before running the instructions, the Docker Daemon validates the file and if the syntax is incorrect, it returns an error. 
- Each run instruction is independent and it causes a new image to be created.
- ***Lets update, install nano, add add executable permission to the hello.sh file***

```  
FROM alpine:3.14 
WORKDIR /usr/src/app/  
COPY hello.sh .
RUN apk update
RUN apk add nano 
RUN chmod +x hello.sh 
``` 

***CMD***
- It defines the commands that will run on the Image at start-up. 
- Unlike a RUN, this does not create a new layer for the Image, but simply runs the command. 
- There can only be one CMD per a Dockerfile/Image. 
- If you need to run multiple commands, the best way to do that is to have the CMD run a script. 
- CMD requires that you tell it where to run the command. 
- ***Lets run our simple script at the start-up***

```  
#Start from the alpine image 
FROM alpine:3.14 

#it defines the working directory
WORKDIR /usr/src/app/  

#copies the script file to the container
COPY hello.sh .

#Installs updates
RUN apk update

#Installs nano text editor
RUN apk add nano 

#it adds executable permission to our script hello.sh
RUN chmod +x hello.sh 

#it runs the script at the start-up
CMD ./hello.sh
``` 
***Read More at [Docker File Reference Page](https://docs.docker.com/engine/reference/builder/)***

### Build your first image
Now that our docker file is ready, we will use ***[docker build](https://docs.docker.com/engine/reference/commandline/build/)*** command to build our image. 
- **docker build** command will look for Dockerfile and build as per instructions in the Dockerfile. 
  - Let's create a directory and save the Dockerfile and hello.sh in the directory.
   - Get into the directory with the CD command and exectue the following command. 

> ***Note: Please use IDE such as Visual Studio code to create Dockerfile. If you use notepad, it adds .txt extension to the file and the build won't work.***

 The following command looks for the Dockerfile. The (.) specifies where to build and -t gives it a name.
```
PS D:\dock1> docker build . -t my-first-image
[+] Building 8.8s (11/11) FINISHED
 => [internal] load build definition from Dockerfile                                                                             0.1s
 => => transferring dockerfile: 438B                                                                                             0.0s
 => [internal] load .dockerignore                                                                                                0.0s
 => => transferring context: 2B                                                                                                  0.0s
 => [internal] load metadata for docker.io/library/alpine:3.14  
```
###### Lets check our image and note the size of the image (my-first-image). It is 15.8MB 
```
PS D:\dock1> docker images
REPOSITORY       TAG       IMAGE ID       CREATED          SIZE
my-first-image   latest    2c03d638427a   46 seconds ago   15.8MB
alpine           latest    9c6f07244728   2 weeks ago      5.54MB
hello-world      latest    feb5d9fea6a5   11 months ago    13.3kB
```
### Run your first image
```
PS D:\dock1> docker run my-first-image       
Hello, World, I am learning to write a Dockerfile!
```

***Lets get the interactive terminal from our image and use nano to create a file called new.txt***
 ```
 PS D:\dock1> docker run -it my-first-image /bin/sh
/usr/src/app # ls
hello.sh
/usr/src/app # nano new.txt/usr/src/app # ls
hello.sh  new.txt
```
### Lets remove the nano from our Dockerfile

```
# Start from the alpine image 
FROM alpine:3.14 

# it defines the working directory
WORKDIR /usr/src/app/  

# copies the script file to the container
COPY hello.sh .

# Installs updates &  it adds executable permission to our script hello.sh (no nano this time)
RUN apk update && \
chmod +x hello.sh 

# it runs the script at the start-up
CMD ./hello.sh
```
##### Now build the image again  as my-first-image2 wihtout nano. Since vi is already available, we don't necessarly need the nano text editor.
```
PS D:\dock1> docker build . -t my-first-image2
[+] Building 3.4s (9/9) FINISHED
 => [internal] load build definition from Dockerfile                                                                             0.0s
 => => transferring dockerfile: 419B                                                                                             0.0s 
 => [internal] load .dockerignore                                                                                                0.0s 
 => => transferring context: 2B                                                                                                  0.0s 
 => [internal] load metadata for docker.io/library/alpine:3.14
 ```

#### Check the size of image

```
PS D:\dock1> docker images
REPOSITORY        TAG       IMAGE ID       CREATED          SIZE
my-first-image2   latest    54ffbbbdf9b1   28 seconds ago   7.76MB
my-first-image1   latest    d7a77ec9a86f   5 minutes ago    15.8MB
my-first-image    latest    4e9767cedcf4   16 minutes ago   15.8MB
```
### Push your first image
Now that you've created and tested your image, you can push it to [Docker Hub](hhttps://hub.docker.com/).

***First you have to login to your Docker Hub account***
```bash
docker login
```
Enter `YOUR_USERNAME` and `password` when prompted. 
```bash
PS D:\dock1> docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: deepakkc
Password: 
Login Succeeded
```
Now all you have to do is:

```bash
PS D:\dock1> docker tag  my-first-image2 deepakkc/my-first-image2
PS D:\dock1> docker push deepakkc/my-first-image2
Using default tag: latest
The push refers to repository [docker.io/deepakkc/my-first-image2]
e6244dc07df3: Pushed
5d6c8546b9b6: Pushed
596c0c0d55cf: Pushed
```
**Now yow you may delete your image and run it again. This time it will pull image from Docker Hub.**

```
PS D:\dock1> docker rmi deepakkc/my-first-image2 -f
Untagged: deepakkc/my-first-image2:latest
Untagged: deepakkc/my-first-image2@sha256:98d5f7439d211156871e8088ae5bf3a2289c9bf00
PS D:\dock1> docker images
REPOSITORY        TAG       IMAGE ID       CREATED         SIZE
<none>            <none>    54ffbbbdf9b1   2 hours ago     7.76MB
my-first-image1   latest    d7a77ec9a86f   2 hours ago     15.8MB
my-first-image    latest    4e9767cedcf4   3 hours ago     15.8MB
alpine            latest    9c6f07244728   2 weeks ago     5.54MB
hello-world       latest    feb5d9fea6a5   11 months ago   13.3kB
PS D:\dock1> docker run deepakkc/my-first-image2
Unable to find image 'deepakkc/my-first-image2:latest' locally
latest: Pulling from deepakkc/my-first-image2
Digest: sha256:98d5f7439263ecbe8088ae5bf3a2289c9b24ea4f00
Status: Downloaded newer image for deepakkc/my-first-image2:latest
Hello, World, I am learning to write a Dockerfile!
PS D:\dock1> docker images
REPOSITORY                 TAG       IMAGE ID       CREATED         SIZE
deepakkc/my-first-image2   latest    54ffbbbdf9b1   2 hours ago     7.76MB
my-first-image1            latest    d7a77ec9a86f   2 hours ago     15.8MB
my-first-image             latest    4e9767cedcf4   3 hours ago     15.8MB
alpine                     latest    9c6f07244728   2 weeks ago     5.54MB
hello-world                latest    feb5d9fea6a5   11 months ago   13.3kB
```
> Note: if you don't have an account, visit [Docker Hub](hhttps://hub.docker.com/) and create an account. 



> ***Now that you are done with this container, stop and remove it since you won't be using it again.***

>**Note:** If you want to learn more about Dockerfiles, check out [Best practices for writing Dockerfiles](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/).

## Exercise
1. Write a Dockerfile to build a Docker image using Ubuntu. Add MySQL database service to your image and push it to DockerHub. Tag the image as username/ubuntu-git:1.1 
2. Run your image from DockerHub, get to the interactive terminal and verify that you have installed MySQL (type `sudo mysql` in terminal, if you get the mysql prompt, it's installed. To exit, type `exit`)
3. Write a Dockerfile to create an image with CMD instruction (any). Use Alpine image as the base image.  
4. Continue working on the Dockerfile created in Task 3. Specify a working directory (WORKDIR as /opt) and use RUN to write "This is my work directory " in a text file test.txt (`echo "This is my work Directory" >test.txt`). Build Docker image as **workdir:v1** and run the image as `docker run -it wordkir:v1 ls`. Make sure that you find **test.txt**. 
5. Write a Docker file to build a Docker image that runs a simple Java app in a Docker container. 
6. Cleanup: You probably don't need any of these containers, therfore you may delete all of them.

***Share your Dockerfile(s) in Moodle discussion thread.***

```
docker rm -f $(docker ps -aq)
```