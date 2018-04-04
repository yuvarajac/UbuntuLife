# Ubuntu Life
This project contains my Ubuntu expriments and commands.

My laptop (from here on referred as Host machine) is running with Windows 10 Pro - x64 model.

To expriment Linux world I need a Linux OS with GUI (so it will be easier for me to start with).

So I decided to setup a VM on my Laptop, I used to use in the past VMWare then HyperV and now Virtual Box. This is the best light weight handly solution for anyone wants to install and setup.

## VitualBox Ubuntu setup
1) Install Virtual Box
```
Follow instructions from here
https://www.virtualbox.org/wiki/Downloads
```
2) Install Ubuntu
```
Pretty simple and straight-forward way to install. 

  a) Download latest Ubunto Desktop ISO format, Mount it as drive (right-click and click Mount). Download any preferred Ubuntu from here.. https://www.ubuntu.com/download/desktop  

  b) Open Virtual Box and create a New VM image follow wizard instructions to complete.

```
## (Optional) Setup Ubuntu for data exchange
You might be wondering why is this required; this comes handy when to share files / contents between Host and Guest OS. 

Open Terminal on Guest VM as we need to perform some commands.
```bash
# Create a directory 
~$ sudo mkdir /mnt/share 
~$ sudo mount -t vboxsf UbuntuShare /mnt/share
```
If it gives you following error message:
```
mount: wrong fs type, bad option, bad superblock on UbuntuShare, missing codepage or helper program, or other error
```
It just means that Virtual Box file-share modules are not installed. Now follow below instructions to install them.   

```bash
~$ sudo apt-get update
~$ sudo install build-essential module-assistant
~$ sudo m-a prepare
```
Then goto **VirtualBox menu > Devices > Install Guest Additions CD image..** follow on screen instructions, after completing the setup again try the mount command, this time it should work.

```bash
~$ sudo mount -t vboxsf UbuntuShare /mnt/share
```

## SSH Setup on Guest OS (Ubuntu VM)

### SSH Client

Usually Ubuntu is packaged with SSH client. So no specific requrement here. But it is good to create SSH Key for the user account on the Guest VM. 

Generate the SSH key for the user who is not a root. And then copy the public key on your Host by using the share we created earlier. As you may know this public key will help in many ways in many places.  

```bash
~$ ssh-keygen
~$ cat .ssh/id_rsa.pub
~$ sudo cp .ssh/id_rsa.pub /mnt/share 
```
### SSH Server

Ubuntu dont come with SSH Server, but having this installed and up and runing is always handy.

```bash
# install SSH server
~$ sudo apt install openssh-server
# setup firewall as Ubuntu uses WFW firewall, it must allow ssh connections 
~$ sudo ufw app list
~$ sudo ufw allow OpenSSH
# enable firewall
~$ sudo ufw enable
# ensure if OpenSSH is allowed by Firewall
~$ sudo ufw status
# to enable SSH key authentication instead of password (which is default) use below commands
~$ cat /etc/ssh/sshd_config
~$ sudo nano /etc/ssh/sshd_config
# change following entries then save & exit (ctl+X then Y then enter) 
PubkeyAuthentication yes
PasswordAuthenticateion no
ChallengeResponseAuthentication no
```

## SSH Setup on Host OS (Windows Laptop)

You don't need to download and install SSH old way anymore on Windows 10. As Windows 10 released it as Windows Features. Just install **SSH Client** feature to use SSH commands on cmd.  

## Ubuntu & Host Networking

Another important step is to create bridge between two hosts. 
By default Virtual Box Ubuntu setup enables private network which will be connected to the Host network using *NAT*. Check the same on Virtual **BOx > Network > Netwotk Adaptor 1 > NAT**. 
This isn't enough, it don't allow you to connect to Guest from Host network. To do so you need to enable port forwarding.
So click on *Port Forwarding* button with following configuraion.
Configure port 22 on 127.0.1.1 (a lookback address of host) and forward any trafic to port 22 on 10.0.2.1 (usually internal address of Guest VM).

This way you may enable SSH to the server with port forwarding.     
