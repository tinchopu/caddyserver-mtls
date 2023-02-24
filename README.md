# caddyserver mTLS example
Sample configurations and steps for making a Caddyfile for mTLS securing a webpage

## Aim of this repository

When trying to setup a Caddyserver for mTLS securing resources, I didn't fine a example highlighting the steps needed.

This aims to be a guide for quickly creating certificates and using them for mTLS securing a resource. A sample caddyfile is provided. The sample file is used to mTLS secure a reverse proxy to google.com

### Generating CA certificate

```bash
openssl req  -new -x509 -nodes -days 365 -subj '/CN=my-ca' -keyout ca.key -out ca.crt
```

In the first line update the Common Name (CN) to something more appropiate in your case. Also please note that this CA certificate will be valid for one year only. This certificate is then referenced [here](https://github.com/tinchopu/caddyserver-mtls/blob/main/Caddyfile#L5)

### Generating Client certificates and signing them with the CA-certificate

```bash
openssl genrsa -out client.key 2048
openssl req -new -key client.key -subj '/CN=my-client' -out client.csr
openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -days 365 -out client.crt
````

Here also change CN to something more appropiate. This will create a CA signed certificate with a validity of 1 year.

Once this is working, you have to import the client certificate to your browser. You should search online since the import can vary depending on your browser.

You will need to create a p12 file and import it. Please note that currently if using a newer openssl implementation they can be issues with the generated p12 file in Mac. In that case, please use the `legacy` flag
```bash
cat client.key client.crt > client.pem
openssl pkcs12 -in client.pem -export -out client.p12
openssl pkcs12 -in client.pem -export -out client_mac.p12 -legacy # Needed in newer implementations of openssl for Mac compatitbility
````