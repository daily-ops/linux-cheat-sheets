### Ignore case when searching for text

```
sed  's/disabled/enabled/gI' values.conf (ignore case)
```

### Replace text only within the specified line numbers, i.e. line #500 and #2000
```
sed -i '500,2000s/enabled/disabled/g' values.conf
```

### Add line at the top of text file (without text editor)

```
$ for i in {1..10}; do echo "line #${i}" >> /tmp/dummy; done
$ sed -i '1s/^/line #0\n/' /tmp/dummy
$ cat /tmp/dummy
```

The `line #0` added to the top

```
line #0
line #1
line #2
line #3
line #4
line #5
line #6
line #7
line #8
line #9
line #10
```

### Delete line that matches regex

```
sed -i '/^127.0.0.1$/d' /home/ansible/.ssh/known_hosts
```

### Delete 1st line:

```
sed '1d' file-name
```

### Delete 10th line:

```
sed '10d' file-name
```

### Delete line # 5 to 10

```
sed '5,10d' file-name
```

### Append path to /etc/environment

```
sed -i -e "/PATH=\"/ s/\"$/:\/usr\/local\/go\/bin\"/g" /etc/environment
```
