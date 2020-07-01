# generate a 2048 bit RSA private key
openssl genpkey -algorithm RSA -out key.pem -pkeyopt rsa_keygen_bits:2048

# generate/output public part of a private key
openssl rsa  -in key.pem -out pub-key.pem

# examine private key

openssl rsa -in key.pem -text

# examine public key

openssl rsa -in pub-key.pem -text -pubin

# generate a certificate request

openssl req -new -key key.pem -out abc.dem

# examine certificate request

openssl req -in abc.dem -text

# generate a self signed certificate

openssl req -x509 -key key.pem -out abc.dem

# display certificate

openssl x509 -in abc.dem -text

