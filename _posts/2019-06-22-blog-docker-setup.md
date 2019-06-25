---
layout: default
permalink: /b/docker-setup
customcss: /assets/css/markdown.css
title: Docker Setup
description: The aim was to be able to host multiple sites on different subdomains from the one server, all covered by SSL without too much hassle. Luckily a lot of the work is already done as there is an image that does almost exactly that, linuxserver/letencrypt. 
image: /assets/images/icons/docker.png
catagories: ['blog']
tags: ['docker', 'nginx', 'reverse proxy', 'portainer']
---

## Docker - SSL, Reverse Proxies, Management and Hosting Web Projects

/The aim was to be able to host multiple sites on different subdomains from the one server, all covered by SSL without too much hassle. Luckily a lot of the work is already done as there is an image that does almost exactly that, [linuxserver/letencrypt](https://hub.docker.com/r/linuxserver/letsencrypt/). 

#### 1 LinuxServer/LetsEncrypt

Docker run command:

```
sudo docker run \
--name=letsencrypt \
--cap-add=NET_ADMIN \
-d \
-e PUID=1000 \
-e PGID=1000 \
-e TZ=Europe/London \
-e URL=weav.ovh \
-e SUBDOMAINS=portainer,test \
-e VALIDATION=http \
-e EMAIL=chris.weaver.work@gmail.com `#optional` \
-e DHLEVEL=2048 `#optional` \
-e ONLY_SUBDOMAINS=false `#optional` \
-e STAGING=false `#optional` \
-p 443:443 \
-p 80:80 \
-v /var/lib/docker/volumes/le-config/_data:/config \
--restart unless-stopped \
linuxserver/letsencrypt
```

Notable information:
- URL is `weav.ovh` and the subdomains `test` and `portainer` are set
- We are binding `/var/lib/docker/volumes/le-config/_data` to `/config` inside the container

When started, the container will generate an SSL certificate using certbot and the information provided as environment variables. All configuration options are stored inside the `/config` directory inside the container, and are accessible wherever you bind mount that volume on the host machine. The container also runs an nginx web server, which is what will be used to set up the reverse proxies to other containers.

At this point if we navigate to the web server via a browser, we should see a page that says ‘Welcome to our server’ alongside a few other things. This is the default page it throws up, it should also be using the SSL certificate it just generated.

#### 2 Portainer

[Portainer](https://www.portainer.io/) is one of the sites I wanted on a subdomain. Portainer is a web based management GUI for Docker, allowing interaction with containers, view logs, start interactive console sessions and view the usage stats of each of them. You can also see networks, images, stacks and tons of other useful information. I find it to be an extremly useful tool when managing a remote instance of Docker.

A basic deployment of Portainer looks something like this:
```
docker volume create portainer_data
```
```
docker run -d -p 9000:9000 \
--name portainer --restart always \
-v /var/run/docker.sock:/var/run/docker.sock \
-v portainer_data:/data \
portainer/portainer
```
Once that is up and running it should be accessible on port 9000, however it will not yet be on a subdomain or have SSL, which is where the linuxserver/letencrypt container comes in again.

#### 3 Reverse Proxies

Alot of the work for setting up reverse proxies is already done in the letsencrypt container, all the configuration for this can be found in the `/config/nginx` directory, including many config files for proxys in `/config/nginx/proxy-confs`.

The portainer proxy configuration is already done and inside of the `proxy-confs` directory, although there are 2 lines I had to change. The first one being `server_name` variable, which I just changed to `server_name portainer.*;` as this is the subdomain I am using for Portainer. The other is a bit further down, `set $upstream_portainer portainer;`. This line is meant to resolve IP address from the container name, but I struggled to get this to work. I ended up setting it directly to the IP address of the Portainer container instead. After that, save, remove the `.sample` from the filename and reload nginx.

I can now navigate to `portainer.weav.ovh` and it proxies me through the nginx server to the portainer container and we are secured with SSL.

The process is similar for almost anything we want to throw up on Docker now, although we might need to write our own reverse proxy config for it. I’ve found the portainer one to be the best starting point for most of the sites I have done. Simply create a proxy configuration file and place it in the `proxy-conf` folder and reload nginx. 

#### 4 Default site

Another thing I wanted to do was change the default site, so what is hosted at `weav.ovh`. This has again mostly been done by the image. In `/config/nginx/site-confs` there is a file called default, this is the file to alter to do that. 

For me this was as simple as removing this line:
```
location / {
    try_files $uri $uri/ /index.html /index.php?$args =404;
}
```
And adding this:
```
location / {
    resolver 127.0.0.11 valid=30s;
    set $upstream_site 172.17.0.4;
    proxy_pass http://$upstream_site:3000;
    proxy_set_header Connection "";
    proxy_http_version 1.1;
    proxy_hide_header X-Frame-Options; # Possibly nott needed after Portainer 1.20.0
}
```

The main bits to note here are the 3rd and 4th lines setting the IP to the container and the port respectively. Again, save and reload nginx and the default web page now has SSL and proxies me through to the other Docker container.

The [linuxserver/letencrypt](https://hub.docker.com/r/linuxserver/letsencrypt/) image contains some other nice features like fail2ban, cronjobs but that's not for today. It should also keep the generated SSL certificate upto date and refresh them when they are close to expiry. Generating a new SSL certificate is also quite easy, you can simply stop the current one and preform the run command again with any new changes i.e. new subdomains. All of the configuration data should be stored in the volume on the host machine, so you won't lose that. With the SSL certificates being inside a volume they can also be mounted and used in other containers.
