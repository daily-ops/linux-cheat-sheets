
* Convert certificate and key into pkcs12 bundle.
  
  ```
  openssl pkcs12 -export -in mycert.crt -inkey mykey.key
                         -out mycert.p12 -name tomcat -CAfile myCA.crt
                         -caname root -chain
  ```

* PKCS#12 (Public Key Cryptography Standards #12) is a binary file format used to store cryptographic objects securely. 
It commonly includes a private key, a certificate (X.509), and optionally, a certificate chain. 
These components are bundled and encrypted with a password for protection. PKCS#12 files are widely used for transporting and installing keys and certificates across systems, often with file extensions like .pfx or .p12.
