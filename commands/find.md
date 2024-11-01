### To find all files modified on the 7th of June, 2007:

```
find . -type f -newermt 2007-06-07 ! -newermt 2007-06-08
```

### To find all files accessed on the 29th of september, 2008:

```
find . -type f -newerat 2008-09-29 ! -newerat 2008-09-30
```

### To find files which had their permission changed on the same day:

```
find . -type f -newerct 2008-09-29 ! -newerct 2008-09-30
```

### To find files which comparatively newer or older than other files

It then uses find with the option -newer to search `-newer` files, alternatively negate the search condition with `-not`

```
find resources/find -name *-oldest.txt -newer resources/find/1st-oldest.txt
find resources/find -name *-oldest.txt -not -newer resources/find/3rd-oldest.txt
```

### The touch command can be used to create a file with specific modified timestamp, such as `0810010000` in which according to the format `[[CC]YY]MMDDhhmm[.ss]`

```
touch -t 0810010000 /tmp/t1
touch -t 0810011000 /tmp/t2
find / -newer /tmp/t1 -and -not -newer /tmp/t2
```

### find and print the top 10 largest files names (not directories) in a particular directory and its sub directories

```
find resources/find -name *-largest.dat -type f -printf '%s %p\n'|sort -nr|head
```

### To restrict the search to the present directory use "-maxdepth 1" with find.

```
find . -maxdepth 1 -printf '%s %p\n'|sort -nr|head
```
