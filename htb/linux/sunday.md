---
description: Learned about a service Finger which can be enumerated for user names
---

# ☀ sunday

## Enumeration

We See port 79 running finger service which we can enumerate using finger-enum script to get usernames.

Port 22022 is also open running SSH ,so we can use it to guess password and using the username

## Initial foot hold&#x20;

We see a username sunny and by trying the password sunday we can get initial foot hold&#x20;

## Priv Esc&#x20;

We see a backup folder in / dir with shadow file with sammys hash

```
$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB
```

Now we search hash example on google and click first website

Them do "ctrl+f" and type $5$ and we see the hash mode is 7400&#x20;

Now we crack it in windows we get&#x20;

cooldude!

## wget Priv

## trool Priv

Once logged in with sammy, I ran sudo -l again to see whether sammy could run anything as root. Fortunately, it seemed sammy could run wget with root privileges. I used wget to read local file system files, specifically the root users crontab and the /root/troll script that sunny had access to. While the output is rather awful even with filtering, I could see that the /root/troll script was using the absolute paths.

The root user crontab showed that there was a job that copies the original troll file back to /root/troll location. I couldn’t workout the frequency that it was doing this as the formatting was all jank but there is definitely a time window in which the exploit needs to run.

```
sudo /usr/bin/wget -i /var/spool/cron/crontabs/root 2>&1
```

```
sudo /usr/bin/wget -i /root/troll 2>&1
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*ZK78FJqFyx0Ag5Lj.png" alt="wget to read system files" height="394" width="700"><figcaption></figcaption></figure>

On my attack box, I created a simple bash script named troll which just executed bash.

```
#!/usr/bin/bash
bash
```

Then, I started a Python web server in the directory where the script was located so that I could download it from the target box.

```
python3 -m http.server 80
```

Once the payload was ready, I opened two terminals. One as the sunny user and one as the sammy user. I prepopulated the sudo /root/troll command on the sunny user. Then on the sammy user, I populated a command to use sudo wget to download and save the payload to /root/troll. Then, in quick succession, I hit return on the wget command, switched to the sunny terminal, and hit return on the /root/troll command. On the second attempt, the payload replaced the /root/troll script with my payload and elevated me to the root user.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/0*fRmio7nl1O0qcH-P.png" alt="Sunday Priv Esc" height="394" width="700"><figcaption></figcaption></figure>

Finally, I was able to capture the root flag and complete the box

OR

## Interesting

Open an nc connection on any port on attacking machine

Then using the wget upload the /etc/sudoers file

Edit the NO passwd section to ALL for sammy

Then download the edit file to /etc/sudoers using sudo wget

Refer to GTFO bins



