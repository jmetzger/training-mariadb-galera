# Add another (no security means) 

## Prerequisite 

  * Node is installed with mariadb 

## Walkthrough 

```
## connect to the node. 

## create configuration from scratch 
# in /etc/mysql/mariadb.conf.d/60-galera.cnf 

[mysqld] 
binlog_format=ROW
innodb_autoinc_lock_mode=2 
bind-address=0.0.0.0

# for better performance // Attention: You might loose data on power outage
innodb_flush_log_at_trx_commit=2

# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster-<your shortcut e.g. r1>"
wsrep_cluster_address="gcomm://10.135.0.7,10.135.0.14,10.135.0.21"

# Galera Synchronization Configuration
```

```
# if we already have a cluster
systemctl restart mariadb 

```
