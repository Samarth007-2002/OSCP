# 🧛 Admirer

## Enumeration <a href="#id-72de" id="id-72de"></a>

In order to hack into a system we need to first gather some information about it. For that, we use Nmap to scan the ports of the target machine to find out what services are running; services that we can target. First let’s try to find out what ports are open and then we will run a detailed scan only on those ports to save time.

```
nmap -p- -T4 admirer.htb --max-retries=2
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*UiCK2VzrX7qEWmQSMzDJtg.png" alt="" height="266" width="700"><figcaption></figcaption></figure>

We have 3 ports open. Let’s run a targeted scan on these ports.

```
nmap -sC -sV -T4 -A -p21,22,80 admirer.htb
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*9O7w0hd7fMrxJPpkVnW2SQ.png" alt="" height="531" width="700"><figcaption></figcaption></figure>

We got to know that we have _vsftpd 3.0.3_ and _Apache 2.4.25_ running on the target. Nmap also tell’s us that there is a disallowed entry in robots.txt and that is _/admin-dir._

Let’s see what we have on the web server and then see the contents of _admin-dir_ also.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*VnI1dRs55-Dt5qD2Ie3rTA.png" alt="" height="367" width="700"><figcaption></figcaption></figure>

We just have a simple web-page with images (Hi Tesla and Darwin! :P)

Let’s see what we have in _/admin-dir_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*O31YnXnkLZkz3xLxGMbc5A.png" alt="" height="212" width="700"><figcaption></figcaption></figure>

We can’t access it. Let’s run a gobuster scan and see if we can have any results of the files or directories inside this directory.

```
gobuster dir -u http://admirer.htb/admin-dir/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,cgi,sh,db,jsp,html,log,txt -t 200 -k --no-error
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*YnQUNr_QMfIn_OpxXK6LHQ.png" alt="" height="373" width="700"><figcaption></figcaption></figure>

We have _contacs.txt_ and _credentials.txt_ in admin-dir.

Let’s see what we have in these text files.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*a1KjVr31uTuo1SScahyfLA.png" alt="" height="593" width="700"><figcaption></figcaption></figure>

Just some emails in _contacts.txt_ file.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*w_V1GEOB_W4GWg1jygITgQ.png" alt="" height="284" width="700"><figcaption></figcaption></figure>

Here we have credentials for FTP user. Let’s login to FTP and see what we have there.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*06f_1Il9s-yx59l1yEQLMA.png" alt="" height="291" width="700"><figcaption></figcaption></figure>

Here we have 2 files. One is _dump.sql_ and the other one is _html.tar.gz._ Looks like it’s a backup of our webserver.

Let’s download these two files using _get_ command of FTP and see what we have in them.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*jf5vPGfUGrRKaym8sqyRIw.png" alt="" height="568" width="700"><figcaption></figcaption></figure>

We have some names for image files here, nothing interesting. But if we extract the _html.tar.gz_ it actually has somethings.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*2Maco3OffTJySeqzrr2L5w.png" alt="" height="134" width="700"><figcaption></figcaption></figure>

The tar package that we extracted gave out some files and directories. Let’s check these directories.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*O_VLkzbm_hJxzKcMi-21Hg.png" alt="" height="136" width="700"><figcaption></figcaption></figure>

Inside _utility-scripts_ we have some php files. Let’s see what we have in _admin\_tasks.php_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*MG7vcWKKiRGYgYMlEpdX4w.png" alt="" height="633" width="700"><figcaption></figcaption></figure>

Judging by the contents, it looks like this php file is used to view logs and create backups of different things. Let’s see if we have this _utility-script_ directory on our webserver.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*4Dm9C_-4VIfRdzgux9ITrg.png" alt="" height="258" width="700"><figcaption></figcaption></figure>

We do! Let’s see if we have _admin\_tasks.php_ inside it as well.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*JIFoVFqFvrDby0bCFJYAuQ.png" alt="" height="330" width="700"><figcaption></figcaption></figure>

And yes we do. I tried playing with this PHP file for a while but it doesn’t seem to be vulnerable due the input whitelisting in the code.

Let’s see if we have other files as well in _utility-scripts._

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*YGDCDUmk2BoJC96WmK_Ypg.png" alt="" height="447" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*VJKh0l1ZsfkLqhWCRhwuTQ.png" alt="" height="173" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*cuTARw7OIwJiS_fNZQAJJg.png" alt="" height="247" width="700"><figcaption></figcaption></figure>

We have _info.php_ and _phptest.php_ but, we don’t seem to have _db\_admin.php._ Which means that the webserver has been modified after the backup was taken in the FTP folder.

Let’s run a gobuster on _utility-scripts_ directory and see what other files or directories we have in there.

After running different wordlists the big.txt from Seclitst Webcontent Discovery found something that I could use.

```
gobuster dir -u http://admirer.htb/utility-scripts/ -w /usr/share/seclists/Discovery/Web-Content/big.txt -x php,cgi,sh,db,jsp,html,log,txt -t 200 -k --no-error
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*qnw-rzUOwdcaPSZfL-0pzw.png" alt="" height="361" width="700"><figcaption></figcaption></figure>

This _adminer.php_ file was not present in the backup that we found on the FTP. Let’s see what we have in this file.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*9918ILT0v-cQmKlrXankrw.png" alt="" height="406" width="700"><figcaption></figcaption></figure>

We have _Adminer 4.6.2_ running here. It shows 2 versions 4.6.8 and 4.8.1. I don’t know why it’s showing 2 versions.

Let’s search for an exploit of 4.6.2

## Initial Foothold <a href="#eb12" id="eb12"></a>

## Admirer exploit

By simple Google search we come to know that _Adminer 4.6.2_ is vulnerable to Arbitrary file read. But the exploitation method is a little different. To do that we need to connect this Adminer to our own MYSQL server.

Let’s setup our MySQL server first.

```
mysql -u root -p
```

Then provide root password.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*zIkk4xc3nY2GpyrB-j3m2w.png" alt="" height="236" width="700"><figcaption></figcaption></figure>

```
CREATE DATABASE daemon_db; USE daemon_db; CREATE TABLE daemon_table (name VARCHAR(5000));
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*cHP1y6-RmHDXhZDVhsl2Lw.png" alt="" height="176" width="700"><figcaption></figcaption></figure>

```
CREATE USER 'daemonexala'@'10.129.177.199' IDENTIFIED BY 'password';
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*hCC95TQHA3J3VtUTmAgi3A.png" alt="" height="72" width="700"><figcaption></figcaption></figure>

```
GRANT ALL PRIVILEGES ON daemon_db.* TO 'daemonexala'@'10.129.177.199';
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*E1jjGTqU2r7NDNXeTXe0hQ.png" alt="" height="74" width="700"><figcaption></figcaption></figure>

Now let’s go to _/etc/mysql/mariadb.conf.d/50-server.cnf_ and change the bind address to our _tun0_ address.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*ocIrKL3IcNHpNMDNHnFMGQ.png" alt="" height="497" width="700"><figcaption></figcaption></figure>

Now let’s login to that _Adminer_ with our server, our user and our database.

<figure><img src="https://miro.medium.com/v2/resize:fit:693/1*-JAYl0aP28d8WBTxHqo8oA.png" alt="" height="435" width="693"><figcaption><p>Adminer login</p></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*rgY3Rjl4dR0oiFG3KCnOWQ.png" alt="" height="467" width="700"><figcaption></figcaption></figure>

We can execute SQL queries by going to _SQL command_.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*Zh8ABaLKWZdOFAi_-v4aLw.png" alt="" height="370" width="700"><figcaption></figcaption></figure>

We are currently in the _utility-script_ directory. We could be able to read index.php using ../index.php. Let’s use SQL query to load that file into our newly created databse.

```
LOAD DATA LOCAL INFILE '../index.php' INTO TABLE daemon_db.daemon_table FIELDS TERMINATED BY "\n"
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*6ZbMplwqCEd5BfxaNuuE4Q.png" alt="" height="250" width="700"><figcaption></figcaption></figure>

And our SQL Query got executed successfully.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*u0yEKgcWVFLVMsV7vmH-hQ.png" alt="" height="357" width="700"><figcaption></figcaption></figure>

We can view our table by going to _daemon\_table_ and then _select data_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*lz69wWEeAAa6BGfo2G54kw.png" alt="" height="382" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*i_oZk6-eUN1ObK5TrNdW4Q.png" alt="" height="235" width="700"><figcaption></figcaption></figure>

Here in our table we have the contents of _index.php_ that contains the credentials of waldo user. Let’s try to SSH into it.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*hO5bmyRhyN0TaIKVe9QxNw.png" alt="" height="258" width="700"><figcaption></figcaption></figure>

And we are in!

## Privilege Escalation <a href="#fe2a" id="fe2a"></a>

Let’s see what commands _waldo_ can run as sudo.

```
sudo -l
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*-5jrKwLdPsuiCYMTaBBE2g.png" alt="" height="138" width="700"><figcaption></figcaption></figure>

It seems like waldo can run the _admin\_tasks.sh_ script with sudo and he can also set environment variables while running that as sudo.

Let’s see what we have in that script.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*d4iemKU07cuHGf6Yst6n9g.png" alt="" height="527" width="700"><figcaption></figcaption></figure>

It’s the same that _admin\_tasks.php_ was running at the backend. If we scroll down we can see that if we run a web backup through this scrip it actually calls on to another python script _/opt/scripts/backup.py_

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*Z0EA48snIWqHF3XShs5Nfw.png" alt="" height="192" width="700"><figcaption></figcaption></figure>

Let’s take a look at that.

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*kqN7buTtJd705POCPOp0KQ.png" alt="" height="114" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*zZcgGyfECM78BzGzB-TOuA.png" alt="" height="255" width="700"><figcaption></figcaption></figure>

That script is importing _make\_archive_ function from _shutil._

Since we can set the environment variables while running _admin\_tasks.sh_ we could also set the _PYTHONPATH_ environment variable to some place that contains our own crafted _shutil.py_ with a function _make\_archive_ in it.

Let’s do that in waldo’s home directory.

<figure><img src="https://miro.medium.com/v2/resize:fit:676/1*jT0tepkXEvllb4_ksC-92A.png" alt="" height="156" width="676"><figcaption></figcaption></figure>

I made my own _shutil.py_ in waldo’s home directory and inside that, I created function _make\_archive_ which actually utilizes _os.system_ to make a connection back to my attacking machine instead of doing any archiving.

Let’s start a listener on our attacking machine and run _admin\_tasks.sh_ with sudo and use _6_ to ask the script to create a backup of webserver for us.

```
sudo PYTHONPATH=/home/waldo /opt/scripts/admin_tasks.sh 6
```

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*WNHEV9Q9gWxem-fixjANhw.png" alt="" height="82" width="700"><figcaption></figcaption></figure>

<figure><img src="https://miro.medium.com/v2/resize:fit:700/1*YUZFNM9Wse1EFuZ1CsctjA.png" alt="" height="128" width="700"><figcaption></figcaption></figure>

And we got a root shell!
