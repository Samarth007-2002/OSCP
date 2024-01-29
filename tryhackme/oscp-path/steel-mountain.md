# Steel Mountain

## Initial Enumeration

```
PORT     STATE SERVICE            VERSION
80/tcp   open  http               Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/8.5
|_http-title: Site doesn't have a title (text/html).
135/tcp  open  msrpc              Microsoft Windows RPC
139/tcp  open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp open  ssl/ms-wbt-server?
| ssl-cert: Subject: commonName=steelmountain
| Not valid before: 2024-01-10T18:46:59
|_Not valid after:  2024-07-11T18:46:59
| rdp-ntlm-info: 
|   Target_Name: STEELMOUNTAIN
|   NetBIOS_Domain_Name: STEELMOUNTAIN
|   NetBIOS_Computer_Name: STEELMOUNTAIN
|   DNS_Domain_Name: steelmountain
|   DNS_Computer_Name: steelmountain
|   Product_Version: 6.3.9600
|_  System_Time: 2024-01-11T18:49:33+00:00
|_ssl-date: 2024-01-11T18:49:38+00:00; +1s from scanner time.
8080/tcp open  http               HttpFileServer httpd 2.3
|_http-title: HFS /
|_http-server-header: HFS 2.3
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
```

We see something interesting in port 8080

let check it in search sploit and use it (hfs 2.30

Lets use this ;

## Initial Foot Hold

Use the exploit

## PRIV sec

1. TRANSFER winpeas
2. In service info we see ASCService with unquoted path and spaces ;so wee exploit it&#x20;
3. stop and start the service using sc start/stop \<service-name>
