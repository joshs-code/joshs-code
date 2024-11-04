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

**<span style="font-size: 19px">WordPress Blog Setup with PODs.</span>**

Create the pod. 

```
podman pod create --name wordpress-pod -p 8080:80
```

**Run maria-db inside the pod:**

\-  d ( Start in the background )

\--pod ( Uses the pod we created above )

\--name mariadb ( The database we will be using )

[docker.io/library/mariadb:latest](https://docker.io/library/mariadb:latest) ( Get the image for mariadb )

*Make sure to replace passwords with strong passwords*

```
podman run -d --pod wordpress-pod --name mariadb \
-e MYSQL_ROOT_PASSWORD=rootpassword \
-e MYSQL_DATABASE=wordpress \
-e MYSQL_USER=wpuser \
-e MYSQL_PASSWORD=wppassword \
docker.io/library\mariadb:latest
```

Run WordPress inside of the pod:

```
podman run -d --pod wordpress-pod --name wordpress \
-e WORDPRESS_DB_HOST=127.0.0.1:3306 \
-e WORDPRESS_DB_USER=wpuser \
-e WORDPRESS_DB_PASSWORD=wppassword \
-e WORDPRESS_DB_NAME=wordpress \
docker.io/library/wordpress:latest
```

Visit your site at:

```
ip a
192.168.12.253:8080
```

**<span style="font-size: 19px">Helpful commands for pods:</span>**

To see the running pods

```
podman pod ps
```

To see the running containers

```
podman ps
```

To display livestream of useful stats. Good for montioring resources:

```
podman stats
```

To display all status not just running ones:

```
podman -a
```

Get detailed information:

```
podman pod inspect <POD NAME>
```

Display Logs:

```
podman logs <CONTAINER NAME>

example: podman logs wordpress
example: podman logs mariadb
```

Display Logs in real time:

```
podman logs -f <CONTAINER NAME>
```

Logs since a certain time:

```
podman logs --since 1hr wordpress
```

Stop Pods:

```
podman stop <pod name>
example: podman stop wordpress_pod
```

Start Pod:

```
podman start <pod name>
example: podman start wordpress_pod
```

Delete Pod:

```
podman rm <pod name>
```

**<span style="font-size: 19px">Data Persistence with Volumes:</span>**

Create a volume:

```
podman volume create <volume name>
example: podman volume create wordpress_data
example: podman volume create mariadb_data
```

List the volumes:

```
podman volume ls
```

Delete a volume:

```
podman volume rm <volume name>
example: podman volume rm wordpress_data
```

Delete all volumes

```
podman volume prune
```

Example podman with persistence: 

( -v mariadb_data:/var/lib/mysql ) /var/lib/mysql is the default area where it stores data files and other data files.

```
podman run -d --pod wordpress-pod --name mariadb \
-v mariadb_data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=rootpassword \
-e MYSQL_DATABASE=wordpress \
-e MYSQL_USER=wpuser \
-e MYSQL_PASSWORD=wppassword \
docker.io/library/mariadb:latest
```

Example podman with persistence 2: 

\- saves the uploads folder

```
podman run -d --pod wordpress-pod --name wordpress \
-v wordpress_data:/var/www/html/wp-content/uploads \
-e WORDPRESS_DB_HOST=127.0.0.1:3306 \
-e WORDPRESS_DB_USER=wpuser \
-e WORDPRESS_DB_PASSWORD=wppassword \
-e WORDPRESS_DB_NAME=wordpress \
docker.io/library/wordpress:latest
```

if you get a error when upload a image on the WordPress page. Change ownership to ww-data

```
# Log into the wordpress container
podman exec -it <CONTAINER ID> /bin/sh
# Change to uploads dir
cd wp-content
# Change ownership to ww-data
chown ww-data:ww-data uploads
```