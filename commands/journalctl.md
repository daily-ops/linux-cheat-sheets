### Display disk usage by journalctl

```
sudo  journalctl --disk-usage
```

```
Archived and active journals take up 3.3G in the file system
```

### Permanently limit disk space consumption

Set the max size `SystemMaxUse` attribute in the journalctl configuration file `/etc/systemd/journald.conf`.

```
[Journal]
#Storage=auto
#Compress=yes
#Seal=yes
#SplitMode=uid
#SyncIntervalSec=5m
#RateLimitIntervalSec=30s
#RateLimitBurst=10000
SystemMaxUse=50M
#SystemKeepFree=
#SystemMaxFileSize=
```

Restart journald service

```
sudo systemctl restart systemd-journald
```

### Truncate journalctl disk usage to a size

```
sudo journalctl --vacuum-size=200M
```

### Query journalctl by date time range

```
 journalctl --since "2025-02-05 00:00:00" --until "2025-02-05 20:00:00"
```

### Query system boots recorded in journalctl

```
sudo journalctl --list-boots
```

Result:

```
0 c684ffa8750a43adabc093bf3b453824 Tue 2025-02-04 15:29:10 AEDT—Thu 2025-02-06 12:37:16 AEDT
```
