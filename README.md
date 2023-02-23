# caddyserver mTLS example
Sample configurations and steps for making a Caddyfile for mTLS securing a webpage

## Aim of this repository

When trying to setup a Caddyserver for mTLS securing resources, I didn't fine a example highlighting the steps needed.

This aims to be a guide for quickly creating certificates and using them for mTLS securing a resource. A sample caddyfile is provided. The sample file is used to mTLS secure a reverse proxy to google.com

### Generating CA / Certificates

```bash
openssl req  -new -x509 -nodes -days 365 -subj '/CN=my-ca' -keyout ca.key -out ca.crt
openssl genrsa -out client.key 2048
openssl req -new -key client.key -subj '/CN=my-client' -out client.csr
openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -days 365 -out client.crt
cat client.key client.crt > client.pem
openssl pkcs12 -in client.pem -export -out client.p12
openssl pkcs12 -in client.pem -export -out client_mac.p12 -legacy # Needed in newer implementations of openssl for Mac compatitbility
````