# 🎮 Blocky

## Enumeration

nmap -sC -sT -sV -O 10.10.10.37

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*3Jh05JDTVqipGl4NQONhEQ.png" alt="" height="453" width="700"><figcaption></figcaption></figure>

On other hand we will run dirbuster scan

Note — When we have multiple application hosted on different port it always useful to run directory bruteforcing with upper and lowercase letters.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*A5N8PoSD61nmAm0kQb9wTA.png" alt="" height="505" width="700"><figcaption></figcaption></figure>

Once i found the above the above directory by bruteforcing i visited each and every directory but plugin directory had some interesting jar file in it as below.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*btZ6fwadfUgy09fae9wVDw.png" alt="" height="466" width="700"><figcaption></figcaption></figure>

### #java/decompiler

So i downloaded this jar file in my system and started to decode it.

There are many tools to decode the jar file but i always like **jd-gui** which is simple drag and drop tool and then we can see the code level of application as below.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*7KjTh6oVtZYABtBKpPKiCg.png" alt="" height="313" width="700"><figcaption></figcaption></figure>

After going through each and every file, The BlockyCore.jar has a one file which give us sql root user and password as below, So we will note this on our notepad for further use.

> Username — root
>
> Password — 8YsqfCTnvxAUeduzjNSXe22

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*rsjYamEDoHQf5lTjrBjFPg.png" alt="" height="345" width="700"><figcaption></figcaption></figure>

Once i got the sql username and password i tried login in to php mysql and i succeed.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*s7X9kz4LgGJ1HadmPKBNYQ.png" alt="" height="348" width="700"><figcaption></figcaption></figure>

Then i tried this same password for PHPMYADMIN, FTP & SSH and i got in ssh using this this username and password.

<figure><img src="https://miro.medium.com/v2/resize:fit:567/1*TKO7rEbsdqQRxX_5ki-_IA.png" alt="" height="218" width="567"><figcaption></figcaption></figure>

So as we go ahead we can now easily extract user flag from the box, for going ahead.

As ippsec’s say you should always do sudo -l & sudo -i before enemurating box for privilege escalation.

So by doing sudo -i and supplying the password we got for the notch user we get in as root user.

<figure><img src="https://miro.medium.com/v2/resize:fit:239/1*PSjTBvTXyNPUXuT2M16zmg.png" alt="" height="51" width="239"><figcaption></figcaption></figure>

User flag -

<figure><img src="https://miro.medium.com/v2/resize:fit:462/1*uma56wPTRehLJivPPkkyIQ.png" alt="" height="67" width="462"><figcaption></figcaption></figure>

Root flag -

<figure><img src="https://miro.medium.com/v2/resize:fit:255/1*hJdlntIBz7F9m3AOqm9HyQ.png" alt="" height="55" width="255"><figcaption></figcaption></figure>

You can also play more with box and try other way out.
