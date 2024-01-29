---
description: '-Linux Priv Python'
---

# WonderLand

## Enumeration&#x20;

Found ssh and some port open

## Web Enumeration

\<ip>/r

then

\<ip>/a

\<ip>/b

gussed \<ip>/r/a/b/b/i/t which was correct

## Initial Foothold

`ctrl+u` found ssh login creds

## Priv Esc

sudo -l

We have priv to access python and an python script&#x20;

We open the python script and we see that it imports random module and as we know that as there is no specific file path it first searches in current directory

so we create python file random.py with code

```
import os

os.system("/bin/bash")
```

then run it as&#x20;

```
sudo -u rabbit <file path in sudo -l output>
```

we get access to rabbit

in rabbit's dir we have a bash script teaparty

we cat it and we can see that it calls date function without an specific file path

so we create date file with code

```
#bin/bash
/bin/bash
```

give it +x permissions

run the teaparty again and we see we have access to hatter and its password is saved in /home/hatter

&#x20;we try sudo -l but nothing found

then we transfer linpeas to tmp and run it

we found that we have perl `CAP_SETUID`

we search gtfo bins for perl then choose capabilities

we get&#x20;

```
cp $(which perl) .
sudo setcap cap_setuid+ep perl

./perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

Then we run in tmp folder

```
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```
