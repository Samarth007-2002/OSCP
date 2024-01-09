# System Enumeration

```
systeminfo
```

grepped

```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"
```

Windows Management Instrumentation - command | `qfe` quick fix engineering&#x20;

`wmic` returns info about system we r running it on| `qfe` returns available patches last patch whats been patched

```
wmic qfe 
```

```
wmic qfe get Caption,Description,HotFixID,InstalledOn
```

kbid- Knowledge Base ID

&#x20;List local disk

```
wmic logicaldisk get caption,description,providername
```

my privileges

```
whoami /priv
```

```
whoami /groups
```

To list current Users

```
net user
```

Print info about a user

```
net user <name of the user>
```

Print Info about a group

```
net localgroup <group name>
```

