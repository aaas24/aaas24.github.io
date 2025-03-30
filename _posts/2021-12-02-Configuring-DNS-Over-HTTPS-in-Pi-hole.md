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
This document describes the steps taken to secure the DNS connection with encryption by using DNS-Over-HTTPS proxy functionality offered by Cloudflared. 

As stated in the [pi-hole documentation](https://docs.pi-hole.net/guides/dns/cloudflared/?h=update#automating-cloudflared-updates): "With standard DNS, requests are sent in plain-text, with no method to detect tampering or misbehavior. This means that not only can a malicious actor look at all the DNS requests you are making (and therefore what websites you are visiting), they can also tamper with the response and redirect your device to resources in their control (such as a fake login page for internet banking).

DNS-Over-HTTPS prevents this by using standard HTTPS requests to retrieve DNS information. This means that the connection from the device to the DNS server is secure and can not easily be snooped, monitored, tampered with or blocked. It is worth noting, however, that the upstream DNS-Over-HTTPS provider will still have this ability."


This project is a follow up from our [installation of pihole in a RaspberryPi 4](https://aaas24.github.io/technology/2021/11/20/Pi-hole.html). The steps are based on Pi-hole Documentation described [here](https://docs.pi-hole.net/guides/dns/cloudflared/?h=update#automating-cloudflared-updates)

#### System Description 
```
pihole -v
  Pi-hole version is v5.6 (Latest: v5.6)
  AdminLTE version is v5.8 (Latest: v5.8)
  FTL version is v5.11 (Latest: v5.11)
```

#### 1.1) Installing cloudflared

Given that we installed 64-bit Raspberry Pi, we  follow the instructions for that OS: 

```bash
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
# Command line args for cloudflared, using Cloudflare DNS
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
  <img src="https://github.com/aaas24/code_library/raw/main/pihole/images/configuring-Upstream-DNS-Servers.jpg" alt="Upstream DNS Servers" width="600">
</p>

When trying to click saved the console threw out an error stating: 
```
"There was a problem applying your settings.
Debugging information:
PHP error (2): Undefined array key "v6_2" in /var/www/html/admin/scripts/pi-hole/php/savesettings.php:212". 
```
At the time I wrote this article, this was an [known issue #1754](https://github.com/pi-hole/AdminLTE/issues/1754) that was scheduled to be fixed in the next release. As it didn't seen to affect the overall internet experience, I decided to leave cloudflare running and schedule its update as a service to ensure that any changes were applied. 


#### 1.4) Configuring Auto updates

###### 1.4.1) Cloudflare
Given that I configured cloudflared manually, I will add /etc/cron.weekly/cloudflared-updater to a weekly cron job. 



You can easily schedule your update using /etc/cron.weekly/yourfile. However, I tend to schedule updates off business hours, typically near weekends when we have more downtime to debug, so I rather control the specific time these jobs run so they don't break the network in the middle of a work day, when it is harder to debug. If you wish to use /etc/cron.weekly, follow the instructions in the documentation. Otherwise, keep reading

- creating a cloudflared updater file and log file

```
sudo touch /etc/default/cloudflared-updater-logs
sudo nano /etc/default/cloudflared-updater
```
Add these lines to the file:
```
arm
sudo systemctl stop cloudflared
sudo cp ./cloudflared-linux-arm /usr/local/bin/cloudflared
sudo chmod +x /usr/local/bin/cloudflared
sudo systemctl start cloudflared
cloudflared -v
sudo systemctl status cloudflared
```
- adjusting the permissions
```
sudo chmod +x /etc/default/cloudflared-updater
sudo chown root:root /etc/default/cloudflared-updater
```
- creating the cronjob
If you are not familiar with cronjob, I suggest reading the manual or [this post](/etc/cron.weekly/cloudflared-updater). In my case, I tend to schedule updates off business hours, typically near weekends when we have more downtime to debug. 

```
crontab -e
```

Add the line: 
```
0 3 * * SAT /etc/default/cloudflared-updater >> /etc/default/cloudflared-updater-logs
```

###### 1.4.1) Pi-hole

- creating a pihole updater file and log file
Pihole automatically logs what happens in the installer. However, I prefer to keep a record of the cronjob updates as well. If you are not interested in doing so, you can simply use add 'pihole -up' to your crontab lists. 

```
sudo touch /etc/default/pihole-updater-logs
sudo nano /etc/default/pihole-updater
```

Add these lines to the file: 

```
echo $date
pihole-up
```

- adjusting the permissions

```
sudo chmod +x /etc/default/pihole-updater
sudo chown root:root /etc/default/pihole-updater
```
- creating a pihole updater log file
Pihole automatically logs what happens in the installer. However, I prefer to keep a record of the cronjob updates as well. If you are not interested in doing so, you can simply use add 'pihole -up' to your crontab lists/

```
crontab -e
```

Add the line: 

```
0 3 * * SAT /etc/default/pihole-updater >> /etc/default/pihole-updater-logs
```
