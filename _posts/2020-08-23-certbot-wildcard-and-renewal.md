---
layout: md
title: Generating & renewing wildcard SSL certificates via certbot with OVH DNS
description: Quick setup for an encrypted USB drive and samba network share
tags: ['SSL', 'cryptography', 'letsencrypt', 'certbot', 'docker']
sort_key: 1
permalink: blog/2020-08-23-certbot-wildcard-and-renewal
---

{% include project-headers.html %}

https://certbot.eff.org/docs/using.html
https://certbot-dns-ovh.readthedocs.io/en/stable/#

sudo apt install python3-certbot-dns-ovh
sudo certbot certonly -n --dns-ovh --dns-ovh-credentials <path-to-credentials> --server https://acme-staging-v02.api.letsencrypt.org/directory -d <domain>,<*.domain> --email <emai> --agree-tos
curl -XPOST -H"X-Ovh-Application: <application key>" -H "Content-type: application/json" https://eu.api.ovh.com/1.0/auth/credential  -d '{"accessRules": [{"method": "POST","path": "/domain/zone/*"},{"method": "DELETE","path": "/domain/zone/*"},{"method": "GET","path": "/domain/zone/*"},{"method": "PUT","path": "/domain/zone/*"}]}'
https://eu.api.ovh.com/createApp/


# OVH API credentials used by Certbot
dns_ovh_endpoint = ovh-eu
dns_ovh_application_key = 
dns_ovh_application_secret = 
dns_ovh_consumer_key = 

chmod 600 file

{"validationUrl":"https://eu.api.ovh.com/auth/?credentialToken=<a token>","state":"pendingValidation","consumerKey":"4lzKpYkPux91wLi434DVdkyj7WLHDyvp"}
