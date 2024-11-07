### Ignore case when searching for text

```
sed  's/disabled/enabled/gI' values.conf (ignore case)
```

### Replace text only within the specified line numbers, i.e. line #500 and #2000
```
sed -i '500,2000s/enabled/disabled/g' values.conf
```

