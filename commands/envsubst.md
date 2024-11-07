### Substitute environment variable in a file. 

```
unset VALUE
cat > /tmp/somefilein.yml <<EOF
key: "${VALUE}"
EOF

export VALUE="the value"

envsubst </tmp/somefilein.yml > /tmp/somefileout.yml
cat /tmp/somefileout.yml
```

The placeholder `${VALUE}` now has been substitued

```
key: "the value"
```
