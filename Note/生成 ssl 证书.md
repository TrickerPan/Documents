# 生成 ssl 证书

首先，创建文件夹。

```bash
$ mkdir /etc/nginx/ssl
```

创建根配置文件并写入配置项。

```bash
$ touch ca.example.cnf
```

```cnf
[ ca ]
default_ca = ca_example

[ ca_example ]
dir           = /etc/nginx/ssl
certs         = /etc/nginx/ssl
new_certs_dir = /etc/nginx/ssl

private_key   = /etc/nginx/ssl/ca.example.key
certificate   = /etc/nginx/ssl/ca.example.crt

default_md    = sha256

name_opt      = ca_default
cert_opt      = ca_default
default_days  = 365
preserve      = no
policy        = policy_loose

[ policy_loose ]
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ req ]
prompt              = no
encrypt_key         = no
default_bits        = 2048
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha256
x509_extensions     = v3_ca

[ v3_ca ]
authorityKeyIdentifier = keyid,issuer
basicConstraints       = critical, CA:true, pathlen:0
keyUsage               = critical, digitalSignature, keyCertSign
subjectKeyIdentifier   = hash

[ server_cert ]
authorityKeyIdentifier = keyid,issuer:always
basicConstraints       = CA:FALSE
extendedKeyUsage       = serverAuth
keyUsage               = critical, digitalSignature, keyEncipherment
subjectAltName         = @alternate_names
subjectKeyIdentifier   = hash

[ req_distinguished_name ]
O  = Your Organization Name
OU = Your Organization Unit
C  = UN
CN = Example Root CA
```

生成根 key 和 crt

```bash
$ openssl genrsa -out "/etc/nginx/ssl/ca.example.key" 4096
$ openssl req -config "/etc/nginx/ssl/ca.example.cnf" -key "/etc/nginx/ssl/ca.example.key" -x509 -new -extensions v3_ca -days 3650 -sha256 -out "/etc/nginx/ssl/ca.example.crt"
```

复制未注释的全局 openssl 配置项。

```bash
$ sed -i '/copy_extensions\ = \ copy/s/^#\ //g' /etc/ssl/openssl.cnf
```

创建普通配置文件并写入配置项。

```bash
$ touch /etc/nginx/ssl/example.cnf
```

```cnf
[ ca ]
default_ca = ca_example

[ ca_example ]
dir           = /etc/nginx/ssl
certs         = /etc/nginx/ssl
new_certs_dir = /etc/nginx/ssl

private_key   = /etc/nginx/ssl/ca.example.key
certificate   = /etc/nginx/ssl/ca.example.crt

default_md    = sha256

name_opt      = ca_default
cert_opt      = ca_default
default_days  = 365
preserve      = no
policy        = policy_loose

[ policy_loose ]
countryName             = optional
stateOrProvinceName     = optional
localityName            = optional
organizationName        = optional
organizationalUnitName  = optional
commonName              = supplied
emailAddress            = optional

[ req ]
prompt              = no
encrypt_key         = no
default_bits        = 2048
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha256
x509_extensions     = v3_ca

[ v3_ca ]
authorityKeyIdentifier = keyid,issuer
basicConstraints       = critical, CA:true, pathlen:0
keyUsage               = critical, digitalSignature, keyCertSign
subjectKeyIdentifier   = hash

[ server_cert ]
authorityKeyIdentifier = keyid,issuer:always
basicConstraints       = CA:FALSE
extendedKeyUsage       = serverAuth
keyUsage               = critical, digitalSignature, keyEncipherment
subjectAltName         = @alternate_names
subjectKeyIdentifier   = hash

[ req_distinguished_name ]
O  = Your Organization Name
OU = Your Organization Unit
C  = UN
CN = example.com

[ alternate_names ]
DNS.1 = example.com
DNS.2 = *.example.com
```

生成普通 key、csr 和 crt 及根 srl

```bash
$ openssl genrsa -out "/etc/nginx/ssl/example.key" 2048
$ openssl req -config "/etc/nginx/ssl/example.cnf" -key "/etc/nginx/ssl/example.key" -new -sha256 -out "/etc/nginx/example.csr"
$ openssl x509 -req -extfile "/etc/nginx/ssl/example.cnf" -extensions server_cert -days 365 -sha256 -in "/etc/nginx/ssl/example.csr" -CA "/etc/nginx/ssl/ca.example.crt" -CAkey "/etc/nginx/ssl/ca.example.key" -CAcreateserial -out "/etc/nginx/ssl/example.crt"
```
