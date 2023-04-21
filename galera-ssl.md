# Galera ssl 

## Encrypting sst 

```
# Easiest is to encrypt mariabackup 


```

## Encrypting ist

```
# Create the certificates 
sudo mkdir /etc/ssl/mysql/
cd /etc/ssl/mysql

# CA Key
sudo openssl genrsa 2048 > ca-key.pem

# Generate the CA certificate file 
sudo openssl req -new -x509 -nodes -days 36500 -key ca-key.pem -out ca.pem

# Create the key file 
sudo openssl req -newkey rsa:2048 -days 36500 -nodes -keyout server-key.pem -out server-req.pem
sudo openssl rsa -in server-key.pem -out server-key.pem

# Create server certificate file 
openssl x509 -req -in server-req.pem -days 36500 -CA ca.pem -CAkey ca-key.pem -set_serial 01 -out server-cert.pem

# Permissions 
sudo chown mysql:mysql /etc/ssl/mysql/ -R
sudo chmod 400 /etc/ssl/mysql/*
sudo chmod 700 /etc/ssl/mysql/

# Copy it to all 3 nodes 

# add the options to all configs 
wsrep_provider_options="socket.ssl_key=/etc/ssl/mysql/server-key.pem;socket.ssl_cert=/etc/ssl/mysql/server-cert.pem;socket.ssl_ca=/etc/ssl/mysql/ca.pem"

```





## Reference 

  * https://www.linuxbabe.com/mariadb/encrypt-replication-traffic-mariadb-galera-cluster-ubuntu
  * https://mariadb.com/kb/en/introduction-to-state-snapshot-transfers-ssts/#rsync
