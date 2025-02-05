## This How-To is aimed to create a simplest authoritative DNS server on Ubuntu.

In this guide, I will use `dummydomain.com` for the SOA record with a few sub-domains.
The network is also simplified to use `192.168.10.0/24` with every host, including DNS server, is located in the same subnet.

1. Install the required software packages.

```
apt install -y bind9
apt install -y bind9utils
apt install -y bind9-doc
apt install -y dnsutils
```

2. Check status of `bind9` system unit.

```
systemctl status bind9
```

Expecting `Active` to show `active (running)`

```
● named.service - BIND Domain Name Server
     Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; preset: enabled)
     Active: active (running) since Mon 2025-02-03 21:45:51 AEDT; 2 days ago
       Docs: man:named(8)
   Main PID: 634 (named)
     Status: "running"
      Tasks: 8 (limit: 1069)
     Memory: 13.8M (peak: 14.3M)
        CPU: 1.875s
     CGroup: /system.slice/named.service
             └─634 /usr/sbin/named -f -u bind

Feb 05 21:38:14 dns-dev named[634]: network unreachable resolving './DNSKEY/IN': 2001:500:1::53#53
Feb 05 21:38:14 dns-dev named[634]: managed-keys-zone: Key 20326 for zone . is now trusted (acceptance timer complete)
Feb 05 21:38:14 dns-dev named[634]: managed-keys-zone: Key 38696 for zone . is now trusted (acceptance timer complete)
```

3. Create options file with `allow-transfer` set to `none` indicates that there is no transfer operation to any secondary.

```
cat >/etc/bind/named.conf.options <<-EOF
options {
        directory "/var/cache/bind";
        recursion no;
        allow-transfer { none; };

        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};

```

4. Create an entry for the forward zone.

```
cat >>/etc/bind/named.conf.local <<-EOF
zone "dummydomain.com" {
    type master;
    file "/etc/bind/zones/db.dummydomain.com";
};
```

5. Create the forward zone

```
mkdir /etc/bind/zones

cp -f /etc/bind/db.local "/etc/bind/zones/db.dummydomain.com"

sed -i 's/root.localhost/admin.dummydomain.com/g' /etc/bind/zones/db.dummydomain.com
sed -i 's/localhost/dummydomain.com/g' /etc/bind/zones/db.dummydomain.com
sed -i 's/(* 2/ 3/g' /etc/bind/zones/db.dummydomain.com

grep -v -E '@(.*)IN(.*)NS(.*)dummydomain.com' /etc/bind/zones/db.dummydomain.com | grep -v -E '@(.*)IN(.*)A(.*)127.0.0.1' | grep -v -E '@(.*)IN(.*)A(.*)::1' > /etc/bind/zones/db.dummydomain.com.tmp
cp -f /etc/bind/zones/db.dummydomain.com.tmp /etc/bind/zones/db.dummydomain.com
echo "; Name servers" >> /etc/bind/zones/db.dummydomain.com
echo "dummydomain.com.    IN      NS      ns1.dummydomain.com." >> /etc/bind/zones/db.dummydomain.com
echo "; A records for name servers" >> /etc/bind/zones/db.dummydomain.com
echo "ns1                IN      A       192.168.10.2" >> /etc/bind/zones/db.dummydomain.com
echo "gitlab             IN      A       192.168.10.10" >> /etc/bind/zones/db.dummydomain.com
echo "jenkins            IN      A       192.168.10.11" >> /etc/bind/zones/db.dummydomain.com
echo "vault              IN      A       192.168.10.12" >> /etc/bind/zones/db.dummydomain.com
```

6. Create the reverse zone file

```
cp -f /etc/bind/db.127 /etc/bind/zones/db.192.168.10
sed -i 's/root.localhost/admin.dummydomain.com/g' /etc/bind/zones/db.192.168.10
sed -i 's/localhost/dummydomain.com/g' /etc/bind/zones/db.192.168.10
sed -i 's/(* 1/ 2/g' /etc/bind/zones/db.192.168.10
grep -v -E '@(.*)IN(.*)NS(.*)dummydomain.com' /etc/bind/zones/db.192.168.10 | grep -v -E '1.0.0(.*)IN(.*)PTR(.*)dummydomain.com' > /etc/bind/zones/db.192.168.10.tmp
cp -f /etc/bind/zones/db.192.168.10.tmp /etc/bind/zones/db.192.168.10
echo "        IN      NS      ns1.dummydomain.com." >> /etc/bind/zones/db.192.168.10
echo "; PTR records" >> /etc/bind/zones/db.192.168.10
echo "1       IN      PTR     ns1.dummydomain.com." >> /etc/bind/zones/db.192.168.10
echo "2       IN      PTR     dev.dns.dummydomain.com." >> /etc/bind/zones/db.192.168.10
echo "3       IN      PTR     gitlab.dummydomain.com." >> /etc/bind/zones/db.192.168.10
echo "4       IN      PTR     jenkins.dummydomain.com." >> /etc/bind/zones/db.192.168.10
echo "5       IN      PTR     vault.dummydomain.com." >> /etc/bind/zones/db.192.168.10
```

7. Check configuration and zone files.

```
named-checkconf
named-checkzone dummydomain.com /etc/bind/zones/db.dummydomain.com
named-checkzone 10.168.192.in-addr.arpa /etc/bind/zones/db.192.168.10
```

8. Restart bind service.

```
systemctl restart bind9
```



## The Ansible playbook below performs the exact same operations.

```
---

- name: "DNS development environment installation playbook"
  vars:
    # A dummy domain name
    dns_domain_name: "dummydomain.com"
    hostname: "dns-dev"
    # List of packages to install
    package_install: [
      { "package": "bind9" },
      { "package": "bind9utils" },
      { "package": "bind9-doc" },
      { "package": "dnsutils" }
    ]
  hosts: dns_dev
  gather_facts: yes
  tasks:
  - name: "checks and debug"
    block:
      - name: "OS check"
        assert:
          that:
          - ansible_os_family == "Debian"
        tags:
          - dns_dev

      - name: "Display hostname"
        debug:
          msg: "inventory_hostname {{ inventory_hostname }}"
        tags:
        - dns_dev

  - name: "Environment preparation"
    become: true
    block:
    - name: "Hostname" 
      shell: |
        hostnamectl set-hostname {{ hostname }}
      tags:
        - dns_dev

  - name: "Software packages"
    become: true
    block:
    - name: Run the equivalent of "apt-get update" as a separate step
      ansible.builtin.apt:
        update-cache: yes
      tags:
      - dns_dev

    - name: "Install packages"
      ansible.builtin.apt:
        name: "{{ item.package }}"
        state: present
      retries: 3
      with_items: "{{ package_install }}"
      tags:
      - dns_dev

    - name: "Post-installation DNS configuration - options"
      shell: |
        #!/bin/bash

        cat >/etc/bind/named.conf.options <<-EOF
        options {
                directory "/var/cache/bind";
                recursion no;
                allow-transfer { none; };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on-v6 { any; };
        };
        EOF
      tags:
      - dns_dev

    - name: "Post-installation DNS configuration - local"
      shell: |
        #!/bin/bash

        cat >>/etc/bind/named.conf.local <<-EOF

        zone "dummydomain.com" {
          type master;
          file "/etc/bind/zones/db.dummydomain.com";
        };
        EOF
      tags:
      - dns_dev

    - name: "Post-installation DNS configuration - zones"
      shell: |
        #!/bin/bash
        
        mkdir /etc/bind/zones

        echo "Processing forward zone file"
        cp -f /etc/bind/db.local "/etc/bind/zones/db.dummydomain.com"
        
        sed -i 's/root.localhost/admin.dummydomain.com/g' /etc/bind/zones/db.dummydomain.com
        sed -i 's/localhost/dummydomain.com/g' /etc/bind/zones/db.dummydomain.com
        sed -i 's/(* 2/ 3/g' /etc/bind/zones/db.dummydomain.com

        grep -v -E '@(.*)IN(.*)NS(.*)dummydomain.com' /etc/bind/zones/db.dummydomain.com | grep -v -E '@(.*)IN(.*)A(.*)127.0.0.1' | grep -v -E '@(.*)IN(.*)A(.*)::1' > /etc/bind/zones/db.dummydomain.com.tmp
        cp -f /etc/bind/zones/db.dummydomain.com.tmp /etc/bind/zones/db.dummydomain.com
        echo "; Name servers" >> /etc/bind/zones/db.dummydomain.com
        echo "dummydomain.com.    IN      NS      ns1.dummydomain.com." >> /etc/bind/zones/db.dummydomain.com
        echo "; A records for name servers" >> /etc/bind/zones/db.dummydomain.com
        echo "ns1                IN      A       192.168.10.2" >> /etc/bind/zones/db.dummydomain.com
        echo "gitlab             IN      A       192.168.10.10" >> /etc/bind/zones/db.dummydomain.com
        echo "jenkins            IN      A       192.168.10.11" >> /etc/bind/zones/db.dummydomain.com
        echo "vault              IN      A       192.168.10.12" >> /etc/bind/zones/db.dummydomain.com

        echo "Processing reverse zone file"
        cp -f /etc/bind/db.127 /etc/bind/zones/db.192.168.10
        sed -i 's/root.localhost/admin.dummydomain.com/g' /etc/bind/zones/db.192.168.10
        sed -i 's/localhost/dummydomain.com/g' /etc/bind/zones/db.192.168.10
        sed -i 's/(* 1/ 2/g' /etc/bind/zones/db.192.168.10
        grep -v -E '@(.*)IN(.*)NS(.*)dummydomain.com' /etc/bind/zones/db.192.168.10 | grep -v -E '1.0.0(.*)IN(.*)PTR(.*)dummydomain.com' > /etc/bind/zones/db.192.168.10.tmp
        cp -f /etc/bind/zones/db.192.168.10.tmp /etc/bind/zones/db.192.168.10
        echo "        IN      NS      ns1.dummydomain.com." >> /etc/bind/zones/db.192.168.10
        echo "; PTR records" >> /etc/bind/zones/db.192.168.10
        echo "1       IN      PTR     ns1.dummydomain.com." >> /etc/bind/zones/db.192.168.10
        echo "2       IN      PTR     dev.dns.dummydomain.com." >> /etc/bind/zones/db.192.168.10
        echo "3       IN      PTR     gitlab.dummydomain.com." >> /etc/bind/zones/db.192.168.10
        echo "4       IN      PTR     jenkins.dummydomain.com." >> /etc/bind/zones/db.192.168.10
        echo "5       IN      PTR     vault.dummydomain.com." >> /etc/bind/zones/db.192.168.10
      tags:
      - dns_dev

    - name: "Post-installation DNS configuration - check and restart"
      shell: |
        named-checkconf
        named-checkzone dummydomain.com /etc/bind/zones/db.dummydomain.com
        named-checkzone 10.168.192.in-addr.arpa /etc/bind/zones/db.192.168.10
        systemctl restart bind9
      tags:
      - dns_dev

```
