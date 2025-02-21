
* Convert certificate and key into pkcs12 bundle.
  
  ```
  openssl pkcs12 -export -in mycert.crt -inkey mykey.key
                         -out mycert.p12 -name tomcat -CAfile myCA.crt
                         -caname root -chain
  ```

* PKCS#12 (Public Key Cryptography Standards #12) is a binary file format used to store cryptographic objects securely. 
It commonly includes a private key, a certificate (X.509), and optionally, a certificate chain. 
These components are bundled and encrypted with a password for protection. PKCS#12 files are widely used for transporting and installing keys and certificates across systems, often with file extensions like .pfx or .p12.

* Setup CA

  ```
  cat >/root/ca/openssl.cnf <<-EOF
  [ ca ]
  # `man ca`
  default_ca = CA_default

  [ CA_default ]
  # Directory and file locations.
  dir               = /root/ca
  certs             = $dir/certs
  crl_dir           = $dir/crl
  new_certs_dir     = $dir/newcerts
  database          = $dir/index.txt
  serial            = $dir/serial
  RANDFILE          = $dir/private/.rand
  
  # The root key and root certificate.
  private_key       = $dir/private/ca.key.pem
  certificate       = $dir/certs/ca.cert.pem
  
  # For certificate revocation lists.
  crlnumber         = $dir/crlnumber
  crl               = $dir/crl/ca.crl.pem
  crl_extensions    = crl_ext
  default_crl_days  = 30
  
  # SHA-1 is deprecated, so use SHA-2 instead.
  default_md        = sha256
  
  name_opt          = ca_default
  cert_opt          = ca_default
  default_days      = 375
  preserve          = no
  policy            = policy_strict

  [ policy_strict ]
  # The root CA should only sign intermediate certificates that match.
  # See the POLICY FORMAT section of `man ca`.
  countryName             = match
  stateOrProvinceName     = match
  organizationName        = match
  organizationalUnitName  = optional
  commonName              = supplied
  emailAddress            = optional

  [ policy_loose ]
  # Allow the intermediate CA to sign a more diverse range of certificates.
  # See the POLICY FORMAT section of the `ca` man page.
  countryName             = optional
  stateOrProvinceName     = optional
  localityName            = optional
  organizationName        = optional
  organizationalUnitName  = optional
  commonName              = supplied
  emailAddress            = optional

  [ req ]
  # Options for the `req` tool (`man req`).
  default_bits        = 2048
  distinguished_name  = req_distinguished_name
  string_mask         = utf8only
  
  # SHA-1 is deprecated, so use SHA-2 instead.
  default_md          = sha256
  
  # Extension to add when the -x509 option is used.
  x509_extensions     = v3_ca

  [ req_distinguished_name ]
  # See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
  countryName                     = Country Name (2 letter code)
  stateOrProvinceName             = State or Province Name
  localityName                    = Locality Name
  0.organizationName              = Organization Name
  organizationalUnitName          = Organizational Unit Name
  commonName                      = Common Name
  emailAddress                    = Email Address
  
  # Optionally, specify some defaults.
  countryName_default             = AU
  stateOrProvinceName_default     = Victoria
  localityName_default            =
  0.organizationName_default      = Example Company
  #organizationalUnitName_default =
  #emailAddress_default           =

  [ v3_ca ]
  # Extensions for a typical CA (`man x509v3_config`).
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid:always,issuer
  basicConstraints = critical, CA:true
  keyUsage = critical, digitalSignature, cRLSign, keyCertSign

  [ v3_intermediate_ca ]
  # Extensions for a typical intermediate CA (`man x509v3_config`).
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid:always,issuer
  basicConstraints = critical, CA:true, pathlen:0
  keyUsage = critical, digitalSignature, cRLSign, keyCertSign

  [ usr_cert ]
  # Extensions for client certificates (`man x509v3_config`).
  basicConstraints = CA:FALSE
  nsCertType = client, email
  nsComment = "OpenSSL Generated Client Certificate"
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid,issuer
  keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
  extendedKeyUsage = clientAuth, emailProtection

  [ server_cert ]
  # Extensions for server certificates (`man x509v3_config`).
  basicConstraints = CA:FALSE
  nsCertType = server
  nsComment = "OpenSSL Generated Server Certificate"
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid,issuer:always
  keyUsage = critical, digitalSignature, keyEncipherment
  extendedKeyUsage = serverAuth

  [ crl_ext ]
  # Extension for CRLs (`man x509v3_config`).
  authorityKeyIdentifier=keyid:always

  [ ocsp ]
  # Extension for OCSP signing certificates (`man ocsp`).
  basicConstraints = CA:FALSE
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid,issuer
  keyUsage = critical, digitalSignature
  extendedKeyUsage = critical, OCSPSigning
  EOF
  ```

  ```
  mkdir -p /root/ca
  cd /root/ca
  mkdir certs crl newcerts private
  chmod 700 private
  touch index.txt
  echo 1000 > serial
  openssl genrsa -aes256 -out private/ca.key.pem 4096
  ```

  ```
  chmod 400 private/ca.key.pem
  
  # Create self-signed root certificate
  openssl req -config openssl.cnf \
      -key private/ca.key.pem \
      -new -x509 -days 7300 -sha256 -extensions v3_ca \
      -subj "/C=AU/ST=Victoria/L=Melbourne/O=Global Security/OU=IT Department/CN=Root CA" \
      -out certs/ca.cert.pem
  
  chmod 444 certs/ca.cert.pem
  ```
  
* Setup Intermediate CA

  ```
  cat >/root/ca/intermediate/openssl.cnf <<-EOF
  [ ca ]
  # `man ca`
  default_ca = CA_default

  [ CA_default ]
  # Directory and file locations.
  dir               = /root/ca/intermediate
  certs             = $dir/certs
  crl_dir           = $dir/crl
  new_certs_dir     = $dir/newcerts
  database          = $dir/index.txt
  serial            = $dir/serial
  RANDFILE          = $dir/private/.rand
  
  # The root key and root certificate.
  private_key       = $dir/private/intermediate.key.pem
  certificate       = $dir/certs/intermediate.cert.pem
  
  # For certificate revocation lists.
  crlnumber         = $dir/crlnumber
  crl               = $dir/crl/intermediate.crl.pem
  crl_extensions    = crl_ext
  default_crl_days  = 30
  
  # SHA-1 is deprecated, so use SHA-2 instead.
  default_md        = sha256
  
  name_opt          = ca_default
  cert_opt          = ca_default
  default_days      = 375
  preserve          = no
  policy            = policy_loose

  [ policy_strict ]
  # The root CA should only sign intermediate certificates that match.
  # See the POLICY FORMAT section of `man ca`.
  countryName             = match
  stateOrProvinceName     = match
  organizationName        = match
  organizationalUnitName  = optional
  commonName              = supplied
  emailAddress            = optional

  [ policy_loose ]
  # Allow the intermediate CA to sign a more diverse range of certificates.
  # See the POLICY FORMAT section of the `ca` man page.
  countryName             = optional
  stateOrProvinceName     = optional
  localityName            = optional
  organizationName        = optional
  organizationalUnitName  = optional
  commonName              = supplied
  emailAddress            = optional

  [ req ]
  # Options for the `req` tool (`man req`).
  default_bits        = 2048
  distinguished_name  = req_distinguished_name
  string_mask         = utf8only
  
  # SHA-1 is deprecated, so use SHA-2 instead.
  default_md          = sha256
  
  # Extension to add when the -x509 option is used.
  x509_extensions     = v3_ca

  [ req_distinguished_name ]
  # See <https://en.wikipedia.org/wiki/Certificate_signing_request>.
  countryName                     = Country Name (2 letter code)
  stateOrProvinceName             = State or Province Name
  localityName                    = Locality Name
  0.organizationName              = Organization Name
  organizationalUnitName          = Organizational Unit Name
  commonName                      = Common Name
  emailAddress                    = Email Address
  
  # Optionally, specify some defaults.
  countryName_default             = AU
  stateOrProvinceName_default     = Victoria
  localityName_default            =
  0.organizationName_default      = Example Company
  #organizationalUnitName_default =
  #emailAddress_default           =

  [ v3_ca ]
  # Extensions for a typical CA (`man x509v3_config`).
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid:always,issuer
  basicConstraints = critical, CA:true
  keyUsage = critical, digitalSignature, cRLSign, keyCertSign

  [ v3_intermediate_ca ]
  # Extensions for a typical intermediate CA (`man x509v3_config`).
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid:always,issuer
  basicConstraints = critical, CA:true, pathlen:0
  keyUsage = critical, digitalSignature, cRLSign, keyCertSign

  [ usr_cert ]
  # Extensions for client certificates (`man x509v3_config`).
  basicConstraints = CA:FALSE
  nsCertType = client, email
  nsComment = "OpenSSL Generated Client Certificate"
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid,issuer
  keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
  extendedKeyUsage = clientAuth, emailProtection

  [ server_cert ]
  # Extensions for server certificates (`man x509v3_config`).
  basicConstraints = CA:FALSE
  nsCertType = server
  nsComment = "OpenSSL Generated Server Certificate"
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid,issuer:always
  keyUsage = critical, digitalSignature, keyEncipherment
  extendedKeyUsage = serverAuth

  [ crl_ext ]
  # Extension for CRLs (`man x509v3_config`).
  authorityKeyIdentifier=keyid:always

  [ ocsp ]
  # Extension for OCSP signing certificates (`man ocsp`).
  basicConstraints = CA:FALSE
  subjectKeyIdentifier = hash
  authorityKeyIdentifier = keyid,issuer
  keyUsage = critical, digitalSignature
  extendedKeyUsage = critical, OCSPSigning
  EOF
  ```
  
  ```
  mkdir /root/ca/intermediate
  cd /root/ca/intermediate
  mkdir certs crl csr newcerts private
  chmod 700 private
  touch index.txt
  echo 1000 > serial
  # crlnumber is used to keep track of certificate revocation lists.
  echo 1000 > /root/ca/intermediate/crlnumber
  ```

  ```
  cd /root/ca
  openssl genrsa -aes256 \
      -out intermediate/private/intermediate.key.pem 4096
  ```

  ```
  cd /root/ca
  openssl req -config intermediate/openssl.cnf -new -sha256 \
      -key intermediate/private/intermediate.key.pem \
      -subj "/C=AU/ST=Victoria/L=Melbourne/O=Global Security/OU=IT Department/CN=Intermediate CA" \
      -out intermediate/csr/intermediate.csr.pem

  cd /root/ca
  openssl ca -config openssl.cnf -extensions v3_intermediate_ca \
      -days 3650 -notext -md sha256 \
      -in intermediate/csr/intermediate.csr.pem \
      -out intermediate/certs/intermediate.cert.pem
  
  chmod 444 intermediate/certs/intermediate.cert.pem

  cat intermediate/certs/intermediate.cert.pem \
      certs/ca.cert.pem > intermediate/certs/ca-chain.cert.pem
  chmod 444 intermediate/certs/ca-chain.cert.pem
  ```
  
* Create a leaf certificate

  ```
  cd /root/ca
  openssl genrsa -aes256 \
      -out intermediate/private/www.example.com.key.pem 2048
  chmod 400 intermediate/private/www.example.com.key.pem
  cd /root/ca
  
  openssl req -config intermediate/openssl.cnf \
      -key intermediate/private/www.example.com.key.pem \
      -subj "/C=AU/ST=Victoria/L=Melbourne/O=Global Security/OU=IT Department/CN=www.example.com" \
      -new -sha256 -out intermediate/csr/www.example.com.csr.pem

  cd /root/ca
  openssl ca -config intermediate/openssl.cnf \
        -extensions server_cert -days 375 -notext -md sha256 \
        -in intermediate/csr/www.example.com.csr.pem \
        -out intermediate/certs/www.example.com.cert.pem
  chmod 444 intermediate/certs/www.example.com.cert.pem
  ```

* Verify leaf certificate against the CA

  ```
  openssl verify -CAfile intermediate/certs/ca-chain.cert.pem \
      intermediate/certs/www.example.com.cert.pem
  ```
