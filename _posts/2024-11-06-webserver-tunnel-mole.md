---
title: "Exposing Web Server with Tunnel Mole"
date: 2024-11-06 00:00:00 +0800
categories: [Projects]
tags: [Projects]
---

# Exposing Local Webserver with Tunnel Mole
**<span style="font-size: 19px">Today we will use Tunnel Mole to expose my web server.</span>**

![Tunnel Mole](https://tunnelmole.com/img/Tunnelmole_Face_40.png)

Here is the link to Tunnel Mole: [tunnelmole.com](https://tunnelmole.com/docs/)

If you haven't already install your web server. I will use Apache:

```
sudo yum install httpd -y 

# Or if using ubuntu

sudo apt install apache2 -y 
```

Next ensure that the server is running:

```
sudo systemctl start httpd.service
sudo systemctl status httpd.service
```

Next use tunnel moles universal installer:

```
curl -O https://install.tunnelmole.com/n3d5g/install && sudo bash install
```

If you want your port to listen to another port besides port 80 you can do that now:

```
vi /etc/apache2/ports.conf
# Change listen port to whatever you want
Listen 8081
```

Finally run the tunnel mole.

```
tmole 8081
```