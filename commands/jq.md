## List

### Convert list to array for using in shell script

Assuming there is a list in a json file as following:

```
cat /tmp/list.json 
[
  "c67657c8-635e-ed45-9e7d-6388ff9cb6eb",
  "ce8c5df8-c949-d325-38b6-821578458ebe",
  "dcc77889-1d6a-0a7c-f699-42fb7bbc90e8",
  "e6df409d-6a92-100a-20c9-aa955e54949c"
]
```

It can be simply converted via `.[]` and a shell loop can make use of the converted array/lines:

```
for item in $(cat /tmp/list.json | jq -r .[]); do echo $item | cut -d '-' -f1 ; done
c67657c8
ce8c5df8
dcc77889
e6df409d
```
