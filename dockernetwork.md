# Docker Networking Basics

The four basic resources of virtualization are CPU, memory, storage and network connection. In this chapter, we go through the various network solutions that are technically available and which solutions are recomended for software developers to implement in their work environment.

## Types of Docker networks 

### **Host contained networks**

`default bridge` The default network driver that will be created when docker installs on the system. All containers get an internal IP address and these containers can access each other, using this internal IP. Default bridge will not support container DNS resolving. For that you need User-defined bridge


`User-defined bridge` networks are best when you need multiple containers to communicate on the same Docker host. Support for internal container DNS resolving


`Host networks` are best when the network stack should not be isolated from the Docker host, but you want other aspects of the container to be isolated. Good chose for network or VPN solution. Unfortunately Host networks **will not** work on Windows computers. 

`none` disable all networking. Usually used in conjunction with a custom network driver or container that don't need network access. 


### **Solutions that require network device management**


`Overlay networks` are best when you need containers running on different Docker hosts to communicate, or when multiple applications work together using swarm services.



`Macvlan networks` are best when you are migrating from a VM setup or need your containers to look like physical hosts on your network, each with a unique MAC address.




https://medium.com/edureka/docker-networking-1a7d65e89013 && https://docs.docker.com/network/ && https://docs.docker.com/network/bridge/





# Docker Networking practice 

In this lab you'll look at the most basic networking components that come with a fresh installation of Docker.

You will complete the following steps as part of this lab.

- [Step 1 - The `docker network` command](#docker_network)
- [Step 2 - List networks](#list_networks)
- [Step 3 - Inspect a network](#inspect)
- [Step 4 - List network driver plugins](#list_drivers)
- [Step 5: Connect a container](#connect-container)
- [Step 6: Test network connectivity](#ping_local)
- [Step 7: Configure NAT for external connectivity](#nat)
- [Step 8: Configure User user-defined bridges](#User)
- [Step 9: Configure Host network (only on Linux machines)](#Host)

# <a name="docker_network"></a>Step 1: The `docker network` command

The `docker network` command is the main command for configuring and managing container networks.

Run a simple `docker network` command from any of your lab machines.

```
$ docker network


Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks

Run 'docker network COMMAND --help' for more information on a command.
```

The command output shows how to use the command as well as all of the `docker network` sub-commands. As you can see from the output, the `docker network` command allows you to create new networks, list existing networks, inspect networks, and remove networks. It also allows you to connect and disconnect containers from networks.

# <a name="list_networks"></a>Step 2: List networks

Run a `docker network ls` command to view existing container networks on the current Docker host.

```
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
1befe23acd58        bridge              bridge              local
726ead8f4e6b        host                host                local
ef4896538cc7        none                null                local
```

The output above shows the container networks that are created as part of a standard installation of Docker.

New networks that you create will also show up in the output of the `docker network ls` command.

You can see that each network gets a unique `ID` and `NAME`. Each network is also associated with a single driver. Notice that the "bridge" network and the "host" network have the same name as their respective drivers.

# <a name="inspect"></a>Step 3: Inspect a network

The `docker network inspect` command is used to view network configuration details. These details include; name, ID, driver, IPAM driver, subnet info, connected containers, and more.

Use `docker network inspect` to view configuration details of the container networks on your Docker host. The command below shows the details of the network called `bridge`.

```
$ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "1befe23acd58cbda7290c45f6d1f5c37a3b43de645d48de6c1ffebd985c8af4b",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Containers": {},
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```

> **NOTE:** The syntax of the `docker network inspect` command is `docker network inspect <network>`, where `<network>` can be either network name or network ID. In the example above we are showing the configuration details for the network called "bridge". Do not confuse this with the "bridge" driver.


# <a name="list_drivers"></a>Step 4: List network driver plugins

The `docker info` command shows a lot of interesting information about a Docker installation.

Run a `docker info` command on any of your Docker hosts and locate the list of network plugins.

```
$ docker info
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  buildx: Docker Buildx (Docker Inc., v0.8.2)
  compose: Docker Compose (Docker Inc., v2.7.0)
  extension: Manages Docker extensions (Docker Inc., v0.2.8)
  sbom: View the packaged-based Software Bill Of Materials (SBOM) for an image (Anchore Inc., 0.6.0)
  scan: Docker Scan (Docker Inc., v0.17.0)

Server:
 Containers: 1
  Running: 0
  Paused: 0
  Stopped: 1
 Images: 5
 Server Version: 20.10.17
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runtime.v1.linux runc io.containerd.runc.v2
 Default Runtime: runc
 Init Binary: docker-init
 ...
```

The output above shows the **bridge**, **host**, **null**, and **overlay** drivers.



# <a name="connect-container"></a>Step 5: Connect a container

The **bridge** network is the default network for new containers. This means that unless you specify a different network, all new containers will be connected to the **bridge** network.

Create a new container.

```
$ docker run --name sleeping-ubuntu -dt ubuntu sleep infinity     
6dd93d6cdc806df6c7812b6202f6096e43d9a013e56e5e638ee4bfb4ae8779ce
```

This command will create a new container named `sleeping-ubuntu` based on the `ubuntu:latest` image and will run the `sleep` command to keep the container running in the background. As no network was specified on the `docker run` command, the container will be added to the **bridge** network. 


Inspect the **bridge** network again to see the new container attached to it.

```
$ docker network inspect bridge
<Snip>
        },
        "ConfigOnly": false,
        "Containers": {
            "d74c80f164a64e4d1d13c195ac1f4acdbe78d7aa13bb5ffde25354639cff8812": {
                "Name": "sleeping-ubuntu",
                "EndpointID": "29ca85f851fcad5bc308a97a82add1072119b075f42bc60156afe27207630a69",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
<Snip>
```

# <a name="ping_local"></a>Step 6: Test network connectivity

The output to the previous `docker network inspect` command shows the IP address of the new container. In the previous example it is "172.17.0.2" but yours might be different.

Ping the IP address of the container from the shell prompt of your Docker host. Remember to use the IP of the container in **your** environment.

```
$ ping 172.17.0.2
64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.069 ms
64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.052 ms
64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.050 ms
64 bytes from 172.17.0.2: icmp_seq=4 ttl=64 time=0.049 ms
64 bytes from 172.17.0.2: icmp_seq=5 ttl=64 time=0.049 ms
^C
--- 172.17.0.2 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 3999ms
rtt min/avg/max/mdev = 0.049/0.053/0.069/0.012 ms
```

Press `Ctrl-C` to stop the ping. The replies above show that the Docker host can ping the container over the **bridge** network.

Log in to the container, install the `ping`
 program and ping `Yle.fi`.

 ```
# Get the ID of the container started in the previous step.
$ docker ps

CONTAINER ID    IMAGE    COMMAND             CREATED  STATUS  NAMES
6dd93d6cdc80    ubuntu   "sleep infinity"    5 mins   Up      sleeping-ubuntu

# Exec into the container
$ docker exec -it 6dd93d6cdc80 /bin/bash

# Update APT package lists and install the iputils-ping package
root@6dd93d6cdc80:/# apt update
 
apt install iputils-ping


# Ping yle.fi from within the container
root@6dd93d6cdc80:/# ping yle.fi           
PING yle.fi (108.156.22.49) 56(84) bytes of data.
64 bytes from 108.156.22.49 (108.156.22.49): icmp_seq=1 ttl=37 time=28.5 ms
64 bytes from 108.156.22.49 (108.156.22.49): icmp_seq=2 ttl=37 time=32.2 ms
64 bytes from 108.156.22.49 (108.156.22.49): icmp_seq=3 ttl=37 time=34.4 ms
64 bytes from 108.156.22.49 (108.156.22.49): icmp_seq=4 ttl=37 time=33.1 ms
^C
--- yle.fi ping statistics ---
7 packets transmitted, 7 received, 0% packet loss, time 6009ms
rtt min/avg/max/mdev = 28.516/33.495/42.324/4.141 ms
```

This shows that the new container can ping the internet and therefore has a valid and working network configuration.


# <a name="nat"></a>Step 7: Configure NAT for external connectivity

In this step we'll start a new **NGINX** container and map port 8080 on the Docker host to port 80 inside of the container. This means that traffic that hits the Docker host on port 8080 will be passed on to port 80 inside the container.

> **NOTE:** If you start a new container from the official NGINX image without specifying a command to run, the container will run a basic web server on port 80.

Start a new container based off the official NGINX image.

```
$ docker run --name web1 -d -p 8080:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
386a066cd84a: Pull complete
7bdb4b002d7f: Pull complete
49b006ddea70: Pull complete
Digest: sha256:9038d5645fa5fcca445d12e1b8979c87f46ca42cfb17beb1e5e093785991a639
Status: Downloaded newer image for nginx:latest
b747d43fa277ec5da4e904b932db2a3fe4047991007c2d3649e3f0c615961038
```

Check that the container is running and view the port mapping.

```
$ docker ps
CONTAINER ID    IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
b747d43fa277   nginx               "nginx -g 'daemon off"   3 seconds ago       Up 2 seconds        443/tcp, 0.0.0.0:8080->80/tcp   web1
6dd93d6cdc80        ubuntu              "sleep infinity"         About an hour ago   Up About an hour                                    sleeping-ubuntu
```

There are two containers listed in the output above. The top line shows the new **web1** container running NGINX. Take note of the command the container is running as well as the port mapping - `0.0.0.0:8080->80/tcp` maps port 8080 on all host interfaces to port 80 inside the **web1** container. This port mapping is what effectively makes the containers web service accessible from external sources (via the Docker hosts IP address on port 8080).

Now that the container is running and mapped to a port on a host interface you can test connectivity to the NGINX web server.

Point your web browser to the IP and port 8080 of your Docker host. The following example shows a web browser pointed to `localhost:8080`. You can also open webpage using Docker Desktop. 

<img src="assets/docker-desktop-open-www.png" title="docker-desktop"> 



If for some reason you cannot open a session from a web browser, you can connect from your Docker host using the `curl` command.

```
$ curl 127.0.0.1:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
    <Snip>
<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

If you try and curl the IP address on a different port number it will fail.

> **NOTE:** The port mapping is actually port address translation (PAT).



# <a name="User"></a>Step 8: Configure User user-defined bridges

So far in the exercises, we have used the default network connection, which has been sufficient for the exercises. However, from the point of view of data security, it is important to isolate different containers from each other if it is not necessary for the containers to talk to each other.

In addition, in user-defined networks, containers can use the names of other containers for DNS addressing, which enables working connections even if the containers' IP space changes. DNS service is critical when we use several different docker containers to form a single service entity, such as a WWW server and Mysql databases



Lets create a user-defined bridge network named 
```
docker network create my-nginx
```


Lets create and start the container. We will start it as a detached process. The -d flag means to start the container detached (in the background). The --rm option means to remove the container once it exits/stops. The -p forwards container 80 port to host 8080 port.

```
docker run --name my-nginx --network my-net -d --rm -p 8080:80 nginx:latest  
```


Next we will stop [Step 6](#ping_local) sleeping-ubuntu container if it still running. If you have already stopped it you can skip to docker network command.


```
$ docker ps

CONTAINER ID    IMAGE    COMMAND             CREATED  STATUS  NAMES
6dd93d6cdc80    ubuntu   "sleep infinity"    5 mins   Up      sleeping-ubuntu


$ docker stop 6dd93d6cdc80
```

We will transfer `sleeping-ubuntu` container from default network to your new user define network, start it and inspect the my-net network for successful connection

```
$ docker network connect my-net sleeping-ubuntu
$ docker start sleeping-ubuntu
$ docker network inspect my-net

<Snip>
                "Name": "my-nginx",
                "EndpointID": "7f69cd1f607744cb38122ad6e884e2fd5dd0d1fdb3f66e01e7c697f70020e7e2",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            },
            "cea4adb35d4d107b531ea9add3884b2250173268afc786ee6c32683cf06eba8a": {
                "Name": "sleeping-ubuntu",
                "EndpointID": "da9266a37c8a5d05d2ea74b3d90826fa3ff5f80f9c80740aadbc1b2f6912bd1c",
                "MacAddress": "02:42:ac:13:00:03",
                "IPv4Address": "172.19.0.3/16",
                "IPv6Address": ""
            }
<Snip>
```


Exec into the container to log in and install the `ping` program if needed.
```
$ docker exec -it sleeping-ubuntu /bin/bash
```


Lets ensure that traffic between containers and DNS names work using the ping command

```
$ root@6dd93d6cdc80:/# ping  my-nginx
PING my-nginx (172.19.0.2) 56(84) bytes of data.
64 bytes from my-nginx.my-net (172.19.0.2): icmp_seq=1 ttl=64 time=0.094 ms
64 bytes from my-nginx.my-net (172.19.0.2): icmp_seq=2 ttl=64 time=0.056 ms
64 bytes from my-nginx.my-net (172.19.0.2): icmp_seq=3 ttl=64 time=0.044 ms
64 bytes from my-nginx.my-net (172.19.0.2): icmp_seq=4 ttl=64 time=0.064 ms
64 bytes from my-nginx.my-net (172.19.0.2): icmp_seq=5 ttl=64 time=0.067 ms
64 bytes from my-nginx.my-net (172.19.0.2): icmp_seq=6 ttl=64 time=0.052 ms
```

This shows that the container  can talk to each other and have a valid connect to the internet. If you want to see my-nginx webpage install curl package using `apt install curl` and `curl my-nginx `

To remove user define network you have to stop containers then remove them from network. 


```
$ docker network disconnect my-net my-nginx 
$ docker network disconnect my-net sleeping-ubuntu
$ docker network rm my-net

```


# <a name="Host"></a>Step 9: Configure Host network (only on Linux machines)

Host network setting can not be run in Windows + Docker Desktop environment because Windows network stack is incompatible to Docker host settings. You can this settings with Linux host using HAMK lab environments or your own linux servers


Lets create and start the container. We will start it as a detached process. The -d flag means to start the container detached (in the background). 

```
$ docker run -d --network host --name hello_nginx nginxdemos/hello
```

Point your web browser to the IP and port 80 of your Docker host. The following example shows a web browser pointed to `localhost:80`. You can also open webpage using Docker Desktop. You will see a basic information of the container. 

```
Server address:	172.17.0.2:80

Server name:	ac7bfe9a3569

Date:	30/Aug/2022:09:53:11 +0000

URI:	/
```



To remove the container and free up port 80 we need to stop container. Container will be removed automatically because of --rm option.

```
$ docker container stop hello_nginx

```


https://docs.docker.com/get-started/docker_cheatsheet.pdf