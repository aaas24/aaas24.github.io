---
layout: post
title:  "Configuring DNS-Over-HTTPS in Pi-hole"
description: "Use Cloudflared DNS-Over-HTTPS functionality to prevent using standard HTTPS requests to retrieve DNS information"
date:   2021-12-02
banner_preview: blog-350-250-raspberry-b&w.jpg
banner_image: blog-1200-400-server-2021-11-22.jpg
category: Technology
tags: [ dataengineering, privacy]
---

# Project Description
This document describes the steps taken to secure the DNS connection with incryption by using DNS-Over-HTTPS proxy functionality offered by Cloudflared. 

As stated in the [pi-hole documentation](https://docs.pi-hole.net/guides/dns/cloudflared/?h=update#automating-cloudflared-updates): "With standard DNS, requests are sent in plain-text, with no method to detect tampering or misbehavior. This means that not only can a malicious actor look at all the DNS requests you are making (and therefore what websites you are visiting), they can also tamper with the response and redirect your device to resources in their control (such as a fake login page for internet banking).

DNS-Over-HTTPS prevents this by using standard HTTPS requests to retrieve DNS information. This means that the connection from the device to the DNS server is secure and can not easily be snooped, monitored, tampered with or blocked. It is worth noting, however, that the upstream DNS-Over-HTTPS provider will still have this ability."


This project is a follow up from our [installation of pihole in a RasberryPi 4](https://aaas24.github.io/technology/2021/11/20/Pi-hole.html). The steps are based on Pi-hole Documentation described [here](https://docs.pi-hole.net/guides/dns/cloudflared/?h=update#automating-cloudflared-updates)


#### 1.1) Installing cloudflared

Given that we installed 64-bit Raspberry Pi, we  follow the instructions for that OS: 

```
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-arm64
sudo cp ./cloudflared-linux-arm64 /usr/local/bin/cloudflared
sudo chmod +x /usr/local/bin/cloudflared
cloudflared -v
```

#### 1.2) Configuring cloudflared to run on startup (manually)

Create a cloudflared user to run the daemon:

```sudo useradd -s /usr/sbin/nologin -r -M cloudflared```

Proceed to create a configuration file for cloudflared:

```sudo nano /etc/default/cloudflared```

Edit configuration file by copying the following in to /etc/default/cloudflared. This file contains the command-line options that get passed to cloudflared on startup:

``` bash
# Commandline args for cloudflared, using Cloudflare DNS
CLOUDFLARED_OPTS=--port 5053 --upstream https://1.1.1.1/dns-query --upstream https://1.0.0.1/dns-query
```

Update the permissions for the configuration file and cloudflared binary to allow access for the cloudflared user:

``` bash
sudo chown cloudflared:cloudflared /etc/default/cloudflared
sudo chown cloudflared:cloudflared /usr/local/bin/cloudflared
```

Then create the systemd script by copying the following into /etc/systemd/system/cloudflared.service. This will control the running of the service and allow it to run on startup:

``` bash
sudo nano /etc/systemd/system/cloudflared.service
```
```
[Unit]
Description=cloudflared DNS over HTTPS proxy
After=syslog.target network-online.target

[Service]
Type=simple
User=cloudflared
EnvironmentFile=/etc/default/cloudflared
ExecStart=/usr/local/bin/cloudflared proxy-dns $CLOUDFLARED_OPTS
Restart=on-failure
RestartSec=10
KillMode=process

[Install]
WantedBy=multi-user.target
```
Enable the systemd service to run on startup, then start the service and check its status:

```
sudo systemctl enable cloudflared
sudo systemctl start cloudflared
sudo systemctl status cloudflared
```

#### 1.3) Configuring Pi-hole

Finally, configure Pi-hole to use the local cloudflared service as the upstream DNS server by logging into the Web Console, selecting "Settings" in the left panel, clicking on "DNS" on the tabs and specifying 127.0.0.1#5053 as the Custom DNS (IPv4), as shown in the image:

<p align="center">
  <img src="https://github.com/aaas24/aaas24.github.io/blob/master/assets/post_files/2021-11-20-Pi-hole/congiguring-Upstream-DNS-Servers" alt="Upstream DNS Servers" width="600">
</p>
