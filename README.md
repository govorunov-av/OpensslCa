# OpensslCa

Для того что бы сертификаты, выпущенные openssl были доверенными необходимо не только создать CA и добавить его в доверенные, но и включать altNames в каждый выпущенный сертификат. Для этого можно прописать это в самом openssl.conf или прописывать это при создании сертификата.

Необходимые блоки в openssl.conf:

    [ v3_req ]
    subjectAltName = @alt_names
    keyUsage = critical, digitalSignature, keyEncipherment
    extendedKeyUsage = serverAuth, clientAuth
    
    [ alt_names ]
    DNS.1 = example.com
    DNS.2 = www.example.com
    IP.1 = 192.168.1.1	#Не обязательно

Опции, при выпуске сертификата, без указания вышеперечисленного:

Способ 1 - При выпуске CSR

    openssl req -new -key server.key -out server.csr -subj "/CN=example.com" \
    -addext "subjectAltName=DNS:example.com,DNS:www.example.com,IP:192.168.1.1"

Способ 2 - При выпуске сертификата 

    openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
    -out server.crt -days 365 \
    -extfile <(printf "subjectAltName=DNS:example.com,DNS:www.example.com,IP:192.168.1.1")


Базовые команды:

Создание CA:

    mkdir /var/lib/openssl/CA/
    cd /var/lib/openssl/CA/
    openssl req -new -key ca.key -out ca.csr -extensions v3_ca
    openssl ca -in ca.csr -out ca.crt -selfsign -extensions v3_ca
