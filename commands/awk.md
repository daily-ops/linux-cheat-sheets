### Split certificates in a single PEM file into multiple files

The awk command will extract the certificates into /tmp/cert.<index>.pem. The `BEGIN` initialize the `c` as in certificate counter and will only increase when the line contains keyword `BEGIN CERT` where the `print` outputs the line into the corresponding files of certificate counter. The `c > 0` is added as a safeguard in case the `PEM` file is not so clean containing some preceeding unexpected lines.

```
awk 'BEGIN {c=0;} /BEGIN CERT/{c++} c > 0 { print > "/tmp/cert." c ".pem"}' < resources/awk/sample_intermediate_cert.pem
```