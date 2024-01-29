# Traverxec

Running NMAP against the open ports

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*gh2Yv_ntRL5k_dpyRJwlvg.png" alt="" height="305" width="700"><figcaption></figcaption></figure>

Port 22 running OpenSSH 7.9p1 and Port 80 running nostromo 1.9.6

Moving to the Web Part

### Port 80 — nostromo 1.9.6 <a href="#id-18c6" id="id-18c6"></a>

Running the IP on the browser

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*6_svhw3D54PE_js-uqKgTQ.png" alt="" height="532" width="1000"><figcaption></figcaption></figure>

We see a webpage related to web development, but since we saw the web server was nostromo 1.9.6 I went to search for potential exploits for it

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*ER7KTx-RACIcNixHR_dWLg.png" alt="" height="114" width="1000"><figcaption></figcaption></figure>

We see 2 exploit, one of them being on metasploit so we will use that one for our ease

Spawning metasploit and searching the module

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*0rzU1yEJW8BRtTFDlWzfFA.png" alt="" height="213" width="1000"><figcaption></figcaption></figure>

Checking and setting the required options

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*FVLlSvRSRRc6lS6KBmyrEw.png" alt="" height="637" width="1000"><figcaption></figcaption></figure>

Now we run the _**exploit**_ command

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*ortiNnHThtHxa_I8ts1iXQ.png" alt="" height="212" width="1000"><figcaption></figcaption></figure>

We got shell on the box as www-data, but since this is an unstable shell , so I wish go for little bit more stable shell by using bash oneliner reverse shell command

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*IZ85eeLrk67JmRFsUJJ34A.png" alt="" height="209" width="1000"><figcaption></figcaption></figure>

Looking back to the netcat listener

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*yU5TUje4acXjoSNJSWrnfg.png" alt="" height="130" width="700"><figcaption></figcaption></figure>

Looking further inside we get a folder for _**nostromo**_

<figure><img src="https://miro.medium.com/v2/resize:fit:566/1*U2vdfb6kxehhwqVhBiaTSw.png" alt="" height="717" width="566"><figcaption></figcaption></figure>

Here we went into the _**conf**_ folder of nostromo and then upon looking on the _**nhttpd.conf**_ file we see that there is a _**.htpasswd**_ file on the _**conf**_ folder

<figure><img src="https://miro.medium.com/v2/resize:fit:551/1*OKvEeyRvLy8m3U1soNFvZA.png" alt="" height="268" width="551"><figcaption></figcaption></figure>

We got a hash along with the username _**david**_ and looking at the hash type in hashcat website

<figure><img src="https://miro.medium.com/v2/resize:fit:539/1*SdZkL_7OYGSoz3QFlU30wA.png" alt="" height="50" width="539"><figcaption></figcaption></figure>

We see that its md5crypt or MD5(Unix) , since we now the mode to use which is 500, I will use hashcat for cracking it

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*WlYX9Xx8j9V-1Br8CAmRHQ.png" alt="" height="475" width="1000"><figcaption></figcaption></figure>

We cracked the password which is _**Nowonly4me**_, trying to login to the user david

<figure><img src="https://miro.medium.com/v2/resize:fit:481/1*iJgTGiaoEgc0A1tMCXIe9Q.png" alt="" height="136" width="481"><figcaption></figcaption></figure>

We see that it fails, remember from the above config file we saw there was a _**public\_www**_ which was listed as _**homedir\_public**_

If you go to the home directory of user _**david**_

<figure><img src="https://miro.medium.com/v2/resize:fit:502/1*N0yTkOgfiMA6RJYDVd9fMQ.png" alt="" height="304" width="502"><figcaption></figcaption></figure>

We see that we cant see the contents of the folder, but if we try to access the _**public\_www**_ folder inside it

<figure><img src="https://miro.medium.com/v2/resize:fit:633/1*liVW2L02Xyn7PqYqy3RerA.png" alt="" height="224" width="633"><figcaption></figcaption></figure>

We got into that folder and also there is one more folder named _**protected-file-area**_ so we try to access that

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*YvNv_OJgdIOkpmeNXHN4jw.png" alt="" height="217" width="700"><figcaption></figcaption></figure>

We see two files named _**.htaccess**_ and _**backup-ssh-identity-files.tgz**_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*L7VMKasbIlt4U_HZbHx4mQ.png" alt="" height="88" width="700"><figcaption></figcaption></figure>

We got a message in .htaccess file , looking into the other file type

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*octOiVYQiJusna6E9Zq-ng.png" alt="" height="82" width="1000"><figcaption></figcaption></figure>

We see its a gzip compressed file, so we will bring it to our box

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*jVcPH82qwL8zDHxDKAhdvg.png" alt="" height="556" width="700"><figcaption></figcaption></figure>

## gzip base64 extraction

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*a5hxkj7B74BpGjROVBSOag.png" alt="" height="96" width="700"><figcaption></figcaption></figure>

So here we used the base64 encoding method to bring the file to our box and now we work on it

<figure><img src="https://miro.medium.com/v2/resize:fit:680/1*a5u1B45am24S-r9awAqQ5A.png" alt="" height="202" width="680"><figcaption></figcaption></figure>

Here we decompressed the gzip tar file and we see that we got ssh keys

<figure><img src="https://miro.medium.com/v2/resize:fit:665/1*a5Std2wsqzNH_vemeKavZg.png" alt="" height="688" width="665"><figcaption></figcaption></figure>

We see that the ssh key is encrypted, so we will have to crack the passphrase

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*pYVdmmPlGo_V7sHY7gwUfw.png" alt="" height="181" width="1000"><figcaption></figcaption></figure>

Now we crack it using John

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*V3FjJDS1C5CxOVNh43ZvRw.png" alt="" height="236" width="700"><figcaption></figcaption></figure>

We cracked it successfully and got the passphrase as _**hunter**_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*cbubJZpX0pER63V_zYnchA.png" alt="" height="100" width="700"><figcaption></figcaption></figure>

We got in successfully and now time to get the user flag

<figure><img src="https://miro.medium.com/v2/resize:fit:338/1*pNvo3BNt-5iXr6bgXsr8dw.png" alt="" height="132" width="338"><figcaption></figcaption></figure>

Moving onto the priv esc part

### Privilege Escalation <a href="#b56e" id="b56e"></a>

Looking into the directory of the user david

<figure><img src="https://miro.medium.com/v2/resize:fit:621/1*1LWppGKB00W-Job2FOkfgA.png" alt="" height="180" width="621"><figcaption></figcaption></figure>

We see a folder named bin in the home directory of user david and also inside that folder are two files one of them being a bash script

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*vNPpfSAATIYaAueMIO2ueg.png" alt="" height="222" width="700"><figcaption></figcaption></figure>

We see that last time which uses sudo, but when we use sudo

<figure><img src="https://miro.medium.com/v2/resize:fit:377/1*1NqMwjueyKbzTNzZ2Ttg8Q.png" alt="" height="59" width="377"><figcaption></figcaption></figure>

It asks for password which we dont have, but when we use the last line command

<figure><img src="https://miro.medium.com/v2/resize:fit:1000/1*LKSL0VyR2WjpRmdP1xeP_w.png" alt="" height="117" width="1000"><figcaption></figcaption></figure>

Upon looking on GTFOBins, I figured it out we could exploit the journalctl binary if it gets us a prompt but for that we need to change out terminal to tty

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*Baxf6WV9VMOrKFsRvsij9A.png" alt="" height="173" width="700"><figcaption></figcaption></figure>

We see we get a prompt at the end of the line, so now we abuse it to get the a shell

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*UgjRchu97OCeeBlKOd8CUQ.png" alt="" height="166" width="700"><figcaption></figcaption></figure>

After pressing enter

<figure><img src="https://miro.medium.com/v2/resize:fit:400/1*LaWPTeEz2pG0SMzw2fwNeA.png" alt="" height="118" width="400"><figcaption></figcaption></figure>

We got root!!! Root flag in the usual place as always

<figure><img src="https://miro.medium.com/v2/resize:fit:249/1*aNO6ezz1W0SAAQAkOWqGOQ.png" alt="" height="68" width="249"><figcaption></figcaption></figure>

Overall a big troll at the end :)
