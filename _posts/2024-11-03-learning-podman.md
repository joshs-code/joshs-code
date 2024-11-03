---
title: "Learning Podman"
date: 2024-11-03 00:00:00 +0800
categories: [Learning, Podman]
tags: [Learning, Podman]
---

# Learning Podman

**<span style="font-size: 21px">Overview of Commands for Podman:</span>**

Grab a image. You can search <https://hub.docker.com/>.

```
podman pull docker.io/alphine
or
podman pull alpine
```

List all images

```
podman images
```

To remove the image

```
podman rmi <IMAGE ID>
```

Start a interactive container: ( The <mark data-color="#ffd43b" style="background-color: #ffd43b; color: inherit">-it </mark>flag allows us to interact with the container shell )

```
podman run -it alpine /bin/sh
```

Open up a interactive shell if you ran it on detached mode:

```
podman exec -it <CONTAINER ID> /bin/sh
```

To list running podman containers:

```
podman ps
```

View all containers even the stopped ons:

```
podman ps -a
```

Stop Container:

```
podman stop <CONTAINER ID>
```

Remove a container:

```
podman rm <CONTAINER ID>
```

Inspect low level information on your container:

```
podman container inspect <CONTAINER NAME>
```

Allow search of unregistered containers:

```
vi /etc/containers/registeries.conf

At the bottom of the file add:
[registries.search]
registries=["docker.io"]
```

**<span style="font-size: 20px">Install Nginx Server on Alpine:</span>**

```
podman pull nxinx:apline
```

Start Alpine Nginx Container: -d (detached "background") 

```
podman run -d --name my_nginx -p 8080:80 nginx:alpine
```

Edit the index.html file and mount it to the container using the -v flag. Specify the location the file you want to share is example ./ is our current dir.:

```
podman run -d --name my_nginx -p 8080:80 -v ./index.html:/usr/share/nginx/html/index.html:ro nginx:alpine
```

Check if the container is running:

```
podman ps
```

Test if the web server is working: 

```
curl http://localhost:8080
```

If the server gets a 403 Forbidden the turn off the SE Linux Enforcing and restart your computer.

```
vi /etc/selinux/config
SELINUX=disabled
```

**<span style="font-size: 19px">PODS:</span>**

What are Pods? A pod is a group of one or more containers that share the same network namespace.