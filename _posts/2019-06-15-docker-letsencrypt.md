---
layout: default
permalink: /b/docker-setup
customcss: /assets/css/markdown.css
title: Docker - SSL, Reverse proxy, Management and hosting web projects
description: Documenting my docker setup, using linuxserver/letencrypt for ssl and reverse proxies.
catagories: ['blog']
---

## Docker
#### SSL, Reverse Proxies, Management and Hosting Web Projects

Quick setup and documentation of my Docker setup. The aim was to be able to host multiple sites on different subdomains from the one server, all covered by SSL without too much hassle. Luckily a lot of the work is already done as there is an image that does almost exactly that, [linuxserver/letencrpyt](https://hub.docker.com/r/linuxserver/letsencrypt/). 

#### LetsEncrypt

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

This container will generate an SSL certificate using certbot and the information provided as environment variables when it starts up. All configuration options are stored at /config inside the container, and are accessible wherever you bind that on the host machine. It also runs a nginx web server which is what will be used to set up the reverse proxies to other pages.

At this point if we navigate to the web server via a browser, we should see a page that says ‘Welcome to our server’ among a few other things. This is the default page it throws up.

#### Portainer

Portainer is one of the sites I wanted on a subdomain. Portainer is a web based GUI for docker, allowing you to do a number of jobs including managing containers, starting interactive shell sessions for running containers, viewing logs, CPU and RAM usage and much more. I find it to be a very useful tool to have for managing remote instances of docker. 

Deploying a basic setup is as easy as this:
```
docker volume create portainer_data
docker run -d -p 9000:9000 --name portainer --restart always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```
After running that, Portainer should be visible on port 9000 now, but it won’t have an SSL certificate or be on a subdomain just yet.

#### Reverse Proxies

Most of the work for reverse proxies has also been done in the letsencrypt container. The container has lots of different nginx config templates for different web services, in this case it also has a portainer one by default. The configuration files can be found at `/config/nginx/proxy-confs`. 

The portainer one is already done, although there are 2 lines I had to change, the first one being `server_name` variable. I have mine set to `server_name portainer.*;` as this is the subdomain I am using for portainer. The other is a bit further down `set $upstream_portainer portainer;`. This line is meant to resolve the container name to the IP address although I struggled to get this to work. I ended up setting it directly to the IP address of the portainer container instead. After that, save, remove the `.sample` from the filename and reload nginx.

I can now navigate to `portainer.weav.ovh` and it proxies me through the nginx server to the portainer container and we are secured with SSL.

The process is similar for almost anything we want to throw up on docker now, although we might need to write our own reverse proxy config for it. I’ve found the portainer one to be the best starting point for most of the sites I have done.

#### Default site

Another thing I wanted to do was change the default site, so what is hosted at `weav.ovh`. This has again mostly been done by the image. In `/config/nginx/site-confs` there is a file called default, this is the file we need to alter, allowing us to setup a reverse proxy to another Docker container.

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

The [linuxserver/letencrpyt](https://hub.docker.com/r/linuxserver/letsencrypt/) image contains some other nice features like fail2ban, cronjobs included but that's not for today.
