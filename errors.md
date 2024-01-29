# ERRORS

## sign\_and\_send\_pubkey: no mutual signature supported SSH

IF ENCOUNTERS THIS ERROR "sign\_and\_send\_pubkey: no mutual signature supported"But the best solution is create a new private key and upload the public key to each server one by one, because when you got this error, means your private key is deprecated to use.

```
# vim ~/.ssh/config, add the lines at the beginning
Host *
    PubkeyAcceptedKeyTypes=+ssh-rsa
    HostKeyAlgorithms=+ssh-rsa
```

OR

```
ssh -o PubkeyAcceptedKeyTypes=ssh-rsa -i {yourfile} user@host
```
