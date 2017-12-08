# zcashd
Zcashd with SSL support

## Deploy

### Generate SSL certs

    openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout server.key -out server.crt
    
### Prepare config

    PASSWORD=`pwgen -nB 10 1`
    tee zcash.conf << EOF
    onlynet=IPv4
    server=1
    rpcuser=rpcuser
    rpcpassword=$PASSWORD
    rpctimeout=30
    rpcport=8332
    rpcconnect=127.0.0.1
    printtoconsole=1
    EOF

### Create confir files and deploy certs

    kubectl create secret generic zcashd-conf --from-file=zcash.conf
    kubectl create secret generic zcashd-ssl --from-file=server.crt --from-file=server.key
    
    git clone https://github.com/kuberstack/zcashd
    $EDITOR zcashd/kubernetes/storage.yaml  # Change volume-ID
    kubectl create -f ./kubernetes/
