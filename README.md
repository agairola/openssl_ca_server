# openssl_ca_server


1) Generate rsa key pair

openssl genrsa -out keys/hostA.key 1024

openssl rsa -inform pem -in keys/hostA.key -text

2) Generate CSR

openssl req -new -key keys/hostA.key -subj /CN=hostA/OU=Test/O=Cisco -outform pem -out csr/hostA.csr

openssl req -in csr/hostA.csr -text

3) Submit the CSR

openssl ca -config config -batch -notext -in csr/hostA.csr -out certs/hostA.pem

cat database

4) Display cert

openssl x509 -inform pem -in certs/hostA.pem -text

5) Export to PKCS#12

openssl pkcs12 -out hostA.p12 -export -inkey keys/hostA.key -passout pass:cisco123 -in certs/hostA.pem -certfile cacert.pem

6)  Convert to/from Base64 

openssl base64 -e -in hostA.p12 -out hostA.b64.p12

7) Verify PKCS#12 contents

openssl pkcs12 -in hostA.p12 -passin pass:cisco123 -passout pass:cisco123 -info
======================================================
openssl genrsa -out keys/HUB1.key 1024
openssl req -new -key keys/HUB1.key -subj /CN=HUB1/OU=Test/O=Cisco -outform pem -out csr/HUB1.csr
openssl ca -config config -batch -notext -in csr/HUB1.csr -out certs/HUB1.pem
openssl pkcs12 -out HUB1.p12 -export -inkey keys/HUB1.key -passout pass:cisco123 -in certs/HUB1.pem -certfile cacert.pem
openssl base64 -e -in HUB1.p12 -out HUB1.b64.p12
======================================================
=================================================================
EKU addition:
=================================================================
openssl genrsa -out keys/Ikev2_ASR.key 2048
openssl req -new -key keys/Ikev2_ASR.key -subj /CN=10.106.44.205/OU=Test/O=Cisco -outform pem -out csr/Ikev2_ASR.csr


openssl ca -config config -extensions server_cert -batch -notext -in csr/Ikev2_ASR.csr -out certs/Ikev2_ASR.pem


openssl pkcs12 -out Ikev2_ASR.p12 -export -inkey keys/Ikev2_ASR.key -passout pass:cisco123 -in certs/Ikev2_ASR.pem -certfile cacert.pem
openssl base64 -e -in Ikev2_ASR.p12 -out Ikev2_ASR.b64.p12
==================================================================

elliptical curve
==================================================================
CA server:

openssl ecparam -out private/cakey.pem -name secp384r1 -genkey
openssl req -new -x509 -sha384 -days 365  -key private/cakey.pem -subj /CN=EC_CA/OU=TAC/O=Cisco -outform pem -out cacert.pem

Client Certificate:

- EG1:

openssl ecparam -out keys/256K1_cert.key -name secp256k1 -genkey
openssl req -new -key keys/256K1_cert.key -subj /CN=256K1_cert/OU=Test/O=Cisco -outform pem -out csr/256K1_cert.csr

openssl ca -config config -batch -notext -in csr/256K1_cert.csr -out certs/256K1_cert.pem

openssl pkcs12 -out 256K1_cert.p12 -export -inkey keys/256K1_cert.key -passout pass:cisco123 -in certs/256K1_cert.pem -certfile cacert.pem
openssl base64 -e -in 256K1_cert.p12 -out 256K1_cert.b64.p12

- EG2

openssl ecparam -out keys/521R1_cert.key -name secp521r1 -genkey
openssl req -new -key keys/521R1_cert.key -subj /CN=521R1_cert/OU=Test/O=Cisco -outform pem -out csr/521R1_cert.csr

openssl ca -config config -batch -notext -in csr/521R1_cert.csr -out certs/521R1_cert.pem

openssl pkcs12 -out 521R1_cert.p12 -export -inkey keys/521R1_cert.key -passout pass:cisco123 -in certs/521R1_cert.pem -certfile cacert.pem
openssl base64 -e -in 521R1_cert.p12 -out 521R1_cert.b64.p12

Encrypt private keys:

openssl ec -in keys/521R1_cert.key -des3

on Router:

crypto pki trustpoint EC
 enrollment terminal pem

===================================
Convert the PEM crl to binary (DER encoded) format:

openssl crl -in crl.pem -out binary.crl -outform DER
Read a binary CRL (will produce same output above):

openssl crl -in binary.crl -inform DER -noout -text
Convert the binary (DER encoded) crl to PEM format:

openssl crl -in binary.crl -inform DER -out crl.pem


 openssl s_client -connect servername:443

~/demoCA $ openssl asn1parse -inform pem -in cacert.pem -i


