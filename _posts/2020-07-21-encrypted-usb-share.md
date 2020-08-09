---
layout: md
title: Creating an encrypted USB network share
description: Quick setup for an encrypted USB drive and samba network share
tags: ['linux', 'cryptography', 'networking', 'samba', 'docker']
sort_key: 1
---

{% include project-headers.html %}

I had a requirement for an encrypted USB drive that could be accessible over my home network when needed. It needed to allow me to mount, decrypt and un-mount quickly, easily and remotely, so I had access to the files on any of my devices (laptops, mobile etc) while VPN'd onto my home network. 

In my case this was to largely be used for key files and other misc files with potentially sensitive imformation in them.

#### Hardware and software

1. Raspberry Pi 4
    - Debian-based distro (I will be using Ubuntu 20.04)
    - Docker
1. 32GB Sandisk USB drive

#### Drive encryption

This will be using `cryptsetup` and `luks` to encrypt the drive. These are quite standard on most linux distrobutions and are often used for creating encrypted volumes, drives etc.

1. Connect the usb
1. Find out where its mounted. `fdisk` can help with this
    - My drive is located at `/dev/sdb`
    
    ```
    fdisk -h

    ...

    Disk /dev/sdb: 29.45 GiB, 31609323520 bytes, 61736960 sectors
    Disk model: Mass-Storage
    Units: sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    ```
1. Wipe the device
    ```
    umount [/dev/sdb]
    wipefs -a [/dev/sdb]
    ```
1. Setup encryption
    - Here I use the `aes-cbc-essiv:sha256` cipher option, there are other more secure choices, this one is often clasified as 'sufficient' and is good enough for what I am doing. 
    - Will be prompted to setup a password at this stage
    
    ```
    cryptsetup -y --cipher aes-cbc-essiv:sha256 --key-size 256 luksFormat [/dev/sdb]
    ```
1. Open the encrypted volume and format
    ```
    cryptsetup luksOpen [/dev/sdb] [volume-name]
    sudo mkfs.ext4 /dev/mapper/[volume-name] -L [volume-name]
    ```
1. Close 
    ```
    cryptsetup luksClose [volume-name]
    ```

#### Samba

There are many different ways to go about using samba, for this I will be using the [`alexandreroman/rpi-samba`](https://github.com/alexandreroman/rpi-samba) docker image, since Im using a Raspberry Pi. This particular image is really easy to use and lightweight.

There are many other docker images for samba, [dperson/samba](https://github.com/dperson/samba) is probably the most notable.

##### [`alexandreroman/rpi-samba`](https://github.com/alexandreroman/rpi-samba)
1. Create a directory where the encrypted volume will be mounted
```
mkdir [samba-volume]
```
1. Clone the repo and `make`, this will build the docker image `rpi-samba`
```
git clone https://github.com/alexandreroman/rpi-samba.git
cd rpi-samba
make
```
1. Create the container
```
docker create -d -it --name samba --restart=unless-stopped -v [samba-volume]:/data/share -p 445:445 rpi-samba
```

#### Finishing up

Now everything is setup! To start everything there are 3 commands & a password prompt:
```
cryptsetup luksOpen [/dev/sdb] [volume-name]
mount /dev/mapper/[volume-name] [samba-volume]
docker start samba
```

and to close it down:
```
docker stop samba
umount [samba-volume]
cryptsetup luksClose [volume-name]
```

Alternativly a script like this can be created to start and stop everything:
```bash
if [ "$1" == "start" ]; then
	cryptsetup luksOpen [/dev/sdb] [volume-name] 
	mount /dev/mapper/[volume-name] [samba-volume]
	docker start samba
elif [ "$1" -eq "stop" ]; then
	docker stop samba
	umount [samba-volume]
    cryptsetup luksClose [volume-name]
	
else
	echo "invalid action. usage: <start|stop>"
fi
```