There are 3 servers involved
- Target server where SSH service is running
- CA server where the keys are generated and stored
- Client machine where user run ssh client

## Option #1

### On CA server

#### Create Users CA and copy the public key to SSH server

Generate CA key pair

```
ssh-keygen -t rsa -b 2048 -m pem -f ssh_ca
```

Transfer the public key to the target SSH server

```
scp ssh_ca.pub target-server:/etc/ssh/ 
```

### On the target SSH server
#### Update /etc/ssh/sshd_config

Add/Update the following entries in ***/etc/ssh/sshd_config***

```
PasswordAuthentication no
TrustedUserCAKeys /etc/ssh/ssh_ca.pub
```

Reload the configuration

```
sudo systemctl daemon-reload
```

#### Restart ssh service

```
sudo systemctl restart ssh
```

### On client machine

#### Create user

On client machine, create the desired user.

```
sudo useradd -d /home/tom -m -s /bin/bash tom
```

#### Create ssh key pair for 

```
ssh-keygen -t rsa -b 2048 -m pem
```

Copy ***/home/tom/.ssh/id_rsa.pub*** to ***/ssh/id_rsa.pub*** on CA server.

### On CA server

#### Sign user ssh public key with ssh_ca private key

```
sudo ssh-keygen -s ssh_ca -I tom_cruise -n tom -V +30m /ssh/id_rsa.pub
```
- The `-n tom` defines the principal. In this simplest setup, it uses the username.
- The `-s ssh_ca` is the private key of CA for signing. 
- The `tom_cruise` is Key ID and will appear in the journal log of SSH service. 
- The option `+30m` will allow the signed public key to be valid for 30 minutes.

Copy the signed public key ***/ssh/id_rsa-cert.pub*** back to client machine at ***/home/tom/.ssh/id_rsa-cert.pub***.

## Option #2

This option is similar to the first option, however, the access is granted to principals instead of individual user. The differences in the configuration are:

### On the target SSH server

#### Create principal files

Make tom part of ***team-a***.

```
sudo mkdir /etc/ssh/auth_principals
echo "team-a" | sudo tee -a /etc/ssh/auth_principals/tom
```

#### Update /etc/ssh/sshd_config

Add/Update the following entries in ***/etc/ssh/sshd_config***

```
AuthorizedPrincipalsFile /etc/ssh/auth_principals/%u
PasswordAuthentication no
TrustedUserCAKeys /etc/ssh/ssh_ca.pub
```

Reload and restart SSH service.

```
sudo systemctl daemon-reload
sudo systemctl restart ssh
```

#### Update /etc/ssh/sshd_config

Add/Update the following entries in ***/etc/ssh/sshd_config***

```
AuthorizedPrincipalsFile /etc/ssh/auth_principals/%u
PasswordAuthentication no
TrustedUserCAKeys /etc/ssh/ssh_ca.pub
```

### On CA server

#### Sign user ssh public key with ssh_ca private key

```
sudo ssh-keygen -s ssh_ca -I tom_cruise -n team-a,team-b -V +30m /ssh/id_rsa.pub
```
- The `-n team-a,team-b` defines the principals to be included.
- The `-s ssh_ca` is the private key of CA for signing. 
- The `tom_cruise` is Key ID and will appear in the journal log of SSH service. 
- The option `+30m` will allow the signed public key to be valid for 30 minutes.

Copy the signed public key ***/ssh/id_rsa-cert.pub*** back to client machine at ***/home/tom/.ssh/id_rsa-cert.pub***.



