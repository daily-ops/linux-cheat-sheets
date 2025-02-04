#### Create a file with heredoc

```
cat >/tmp/test<<-EOF
file content line 1
file content line 2
EOF
```

Result in:

```
cat /tmp/test 
file content line 1
file content line 2
```
