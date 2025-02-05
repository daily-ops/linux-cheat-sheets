### Partition and create LVM mount

```
parted /dev/xvdb mklabel gpt
pvcreate /dev/xvdb
vgcreate data /dev/xvdb
lvcreate  -l 100%VG -n data_volume -y data 
mkfs.ext4 /dev/data/data_volume
```
