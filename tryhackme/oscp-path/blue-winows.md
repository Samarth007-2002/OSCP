---
description: RECON
---

# Blue-Winows

```
nmap -sV -sC --script vuln -oN blue.nmap 10.10.195.144
```

found an vul smb-vuln-ms17-010(buffer overflow)

searched it on msfconsole using search ms17-010

run it and get access

\---------------------------------------------------------------------------------------------------------------------

In case u dont get an admin access while using a different method(check by running getuid) then use exploit (post/multi/manage/shell\_to\_meterpreter)

\---------------------------------------------------------------------------------------------------------------------

PRIV ESC&#x20;



```
ps (to print all proccess)
```

```
migrate <ID> 
```

Migrate to higher priv like spoolsv.exe as it has admin priv and gets recreated if deleted&#x20;

Much stable connection

\---------------------------------------------------------------------------------------------------------------------

Crack

hashdump to dump hashes&#x20;

Crack password&#x20;

save the john hash into a text file then crack

```
john john.hash --format=NT --wordlist= /root/wordlists/rockyou.txt
```

\---------------------------------------------------------------------------------------------------------------------

Flag1? _This flag can be found at the system root._

&#x20;          _-Ans C drive_

Flag2? This flag can be found at the location where passwords are stored within Windows.

\*Errata: Windows really doesn't like the location of this flag and can occasionally delete it. It may be necessary in some cases to terminate/restart the machine and rerun the exploit to find this flag. This relatively rare, however, it can happen.

&#x20;            \-Ans C>WINDOWS?SYSTEM32 >CONFIG>FLAG2.TXT

flag3? _This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved._

&#x20;             _-Ans Users then John then flag3.txt_
