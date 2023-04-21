# Cluster Configuration (ubuntu/debian - MariaDB 10.6 from mariadb.org) 

## Node 1 

```
# cat /etc/mysql/mariadb.conf.d/60-galera.cnf
[mysqld]
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2 
bind-address=0.0.0.0

# Set to 1 sec instead of per transaction

# for better performance // Attention: You might loose data on power outage
innodb_flush_log_at_trx_commit=2

# Galera Provider Configuration

wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so

# Galera Cluster Configuration
wsrep_cluster_name="test_cluster-<your shortcut e.g. r1>"
wsrep_cluster_address="gcomm://10.135.0.x"

# Galera Synchronization Configuration
wsrep_sst_method=rsync
```

```
# start 1st cluster node 
systemctl stop mariadb
# delete the binary logs in /var/lib/mysql/
# rm /var/lib/mysql/mysqld-bin* 
galera_new_cluster # always use this to bootstrap first cluster node 

systemctl status mariadb 
journalctl -u mariadb 

```

