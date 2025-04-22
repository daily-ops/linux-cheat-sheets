- Find swap usage for each process

```
 for file in /proc/*/status; do    awk '/VmSwap|Name/{printf $2 " " $3}END{ print ""}' $file ; done
```
