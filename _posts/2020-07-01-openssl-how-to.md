# abbreviation
PEM = privary enhanced electronic mail

pkcs #12 = public key cryptography standard #12

# generate a 2048 bit RSA private key
openssl genpkey -algorithm RSA -out key.pem -pkeyopt rsa_keygen_bits:2048

# generate/output public part of a private key
openssl rsa  -in key.pem -out pub-key.pem

# examine private key
openssl rsa -in key.pem -text

# examine public key
openssl rsa -in pub-key.pem -text -pubin

# generate a certificate request
openssl req -new -key key.pem -out abc.pem

# examine certificate request
openssl req -in abc.pem -text

# generate a self signed certificate
openssl req -x509 -new -key key.pem -out abc.pem

# display certificate
openssl x509 -in abc.pem -text

# export to p12 file
openssl pkcs12 -export -in abc.pem -inkey key.pem -out key.p12

