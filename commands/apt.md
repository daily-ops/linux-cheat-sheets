### List available versions

#### Option #1 

```
sudo apt-cache madison postgresql
```

Return with the list of versions, however i386 also returned.

```
postgresql | 12+214ubuntu0.1 | http://au.archive.ubuntu.com/ubuntu focal-updates/main amd64 Packages
postgresql | 12+214ubuntu0.1 | http://au.archive.ubuntu.com/ubuntu focal-updates/main i386 Packages
postgresql | 12+214ubuntu0.1 | http://security.ubuntu.com/ubuntu focal-security/main amd64 Packages
postgresql | 12+214ubuntu0.1 | http://security.ubuntu.com/ubuntu focal-security/main i386 Packages
postgresql |     12+214 | http://au.archive.ubuntu.com/ubuntu focal/main amd64 Packages
postgresql |     12+214 | http://au.archive.ubuntu.com/ubuntu focal/main i386 Packages
```

#### Option #2

```
apt list -a postgresql
```

Return with the list of versions that belong to OS architecture.

```
Listing... Done
postgresql/focal-updates,focal-updates,focal-security,focal-security 12+214ubuntu0.1 all
postgresql/focal,focal 12+214 all
```
