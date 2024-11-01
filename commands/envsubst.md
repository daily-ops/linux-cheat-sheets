## Substitute environment variable in a file. 

```
$cat somefileint.yml
key: "${VALUE}"

$export VALUE="the value"

$envsubst <somefilein.yml > /tmp/somefileout.yml
$cat /tmp/somefileout.yml
key: "the value"
```
