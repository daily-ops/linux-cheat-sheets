### Generate key pair with ed25519 algorithm in PEM format output the file to /tmp/id_ed25519

```
ssh-keygen -t ed25519 -m pem -f /tmp/id_ed25519
```

Some tools has compatibility issue/default value issue in which `-m` will help to ensure that the output is in the right format.
