# Galera ssl 

## Encrypting sst 

```
# Easiest is to encrypt mariabackup 
# Best docs 
https://www.linuxbabe.com/mariadb/encrypt-replication-traffic-mariadb-galera-cluster-ubuntu

```

## Encrypting ist

### Step 1: tearing down nodes 

```
on all nodes 
systemctl stop mariadb 
# and on last node too
check if safe_to _bootstrap in /var/lib/mysql/grastate.dat 
```

## Step 2: Create certificates on first node and configure 60-galera.cnf 

```
# Create the certificates 
sudo mkdir /etc/ssl/mysql/
cd /etc/ssl/mysql

# CA Key
sudo openssl genrsa 2048 > ca-key.pem

# Generate the CA certificate file 
# Attention: use common_name e.g. : CA 
sudo openssl req -new -x509 -nodes -days 36500 -key ca-key.pem -out ca.pem

# Create the key file 
# Attention: use common_name .e.g. : GaleraNode 
sudo openssl req -newkey rsa:2048 -days 36500 -nodes -keyout server-key.pem -out server-req.pem
sudo openssl rsa -in server-key.pem -out server-key.pem

# Create server certificate file 
openssl x509 -req -in server-req.pem -days 36500 -CA ca.pem -CAkey ca-key.pem -set_serial 01 -out server-cert.pem

# Permissions 
sudo chown mysql:mysql /etc/ssl/mysql/ -R
sudo chmod 400 /etc/ssl/mysql/*
sudo chmod 700 /etc/ssl/mysql/


# add the options to all configs 
wsrep_provider_options="socket.ssl_key=/etc/ssl/mysql/server-key.pem;socket.ssl_cert=/etc/ssl/mysql/server-cert.pem;socket.ssl_ca=/etc/ssl/mysql/ca.pem"

```

```
# Start the first node 
galera_new_cluster 
```

## Step 3: copy certificates to 2nd node 

```
cd /etc/ssl 
tar cvfz mysql-sql.tar.gz mysql 
scp mysql-sql.tar.gz 11trainingdo@10.135.0.14:/tmp 

# on .14 copy from tmp as root
sudo su -
cp -a /tmp/mysql-sql.tar.gz /etc/ssl 
cd /etc/ssl 
tar xvf mysql-sql.tar.gz 

```

## Step 4: change configuration node 2 including ssl 

```
# add the options to all configs 
wsrep_provider_options="socket.ssl_key=/etc/ssl/mysql/server-key.pem;socket.ssl_cert=/etc/ssl/mysql/server-cert.pem;socket.ssl_ca=/etc/ssl/mysql/ca.pem"
```

```
systemctl start mariadb 
```

## Step 5: copy certificates to 3nd node 

```
cd /etc/ssl 
tar cvfz mysql-sql.tar.gz mysql 
scp mysql-sql.tar.gz 11trainingdo@10.135.0.14:/tmp 

# on .14 copy from tmp as root
sudo su -
cp -a /tmp/mysql-sql.tar.gz /etc/ssl 
cd /etc/ssl 
tar xvf mysql-sql.tar.gz 

```

## Step 6: change configuration node 3 including ssl 

```
# add the options to all configs 
wsrep_provider_options="socket.ssl_key=/etc/ssl/mysql/server-key.pem;socket.ssl_cert=/etc/ssl/mysql/server-cert.pem;socket.ssl_ca=/etc/ssl/mysql/ca.pem"
```

```
systemctl start mariadb 
```


## Reference 

  * https://www.linuxbabe.com/mariadb/encrypt-replication-traffic-mariadb-galera-cluster-ubuntu
  * https://mariadb.com/kb/en/introduction-to-state-snapshot-transfers-ssts/#rsync
