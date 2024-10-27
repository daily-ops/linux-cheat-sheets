## Using alternate SSH identity file in git command

By default git command uses $HOME/.ssh/id_rsa for private key identity file. There may be such circumstances that require you to have private key files different from the default name. The command below will add `sshCommand` entry to `.git/config` and allow git command to use the specify identity file.

```
git config --local core.sshCommand "/usr/bin/ssh -i /home/<USER>/.ssh/<"
```

Another scenario that you may encounter is when having to access multiple git server from the same server and user where they are configured with different private/public keys. The `.ssh/config` file allows customization for each individual target host to use different private key file as below.

```
Host github.com
  IdentityFile ~/.ssh/id_rsa.github

Host localgit.localdomain
  IdentityFile ~/.ssh/id_rsa.local
```
