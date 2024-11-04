## Generate Random Numbers/IDs

### UUID

```
$ uuidgen
99cb92d0-db7d-4710-af7b-0c86c764de6b
````

### Random Hex

Reading 4 bytes from `/dev/urandom` and convert the binaries to hex representation using `xxd` command results in 8 digit hex.

```
$ head -c 4 /dev/urandom | xxd -p
c9ed7acf
````
