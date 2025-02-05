### Set default DROP policy on the INPUT chain

```
iptables -P INPUT DROP
```

### List all rules with details and numbers

```
iptables -L -v -n --line-numbers
```

### Insert rule into INPUT chain

```
iptables -I INPUT 1 -s 192.168.0.0/24 -d 192.168.0.0/24 -p tcp --dport 443 -j ACCEPT
```

### Delete rule from INPUT chain

```
iptables -D INPUT 1
```

### Connection logs

```
tail -f /var/log/syslog
```
