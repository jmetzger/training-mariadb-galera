# Installation percona xtradb cluster (5.7.) 

## Install Repo 

```
# as root 
apt update 
apt install curl
curl -O https://repo.percona.com/apt/percona-release_latest.generic_all.deb

apt install gnupg2 lsb-release ./percona-release_latest.generic_all.deb
apt update
```

## Install xtradb 

```
# Find out which packages to configure
percona-release show 
# setup the repos 
percona-release setup pxc57

# apt 
apt install percona-xtradb-cluster-57
```

## Configuration xtradb cluster (first node) 

```
# /etc/percona-xtradb-cluster.conf.d/wsrep.cnf
# path changed in pxc 8.x 
[mysqld]
wsrep_provider=/usr/lib/libgalera_smm.so
wsrep_cluster_name=pxc-cluster
# change that
# wsrep_cluster_address=gcomm://192.168.70.61,192.168.70.62,192.168.70.63
wsrep_node_name=pxc1
# change that 
# wsrep_node_address=192.168.70.61
wsrep_sst_method=xtrabackup-v2
wsrep_sst_auth=sstuser:passw0rd
pxc_strict_mode=ENFORCING
binlog_format=ROW
default_storage_engine=InnoDB
innodb_autoinc_lock_mode=2
```


## Start first node (bootstrapping) 

```
systemctl start mysql@bootstrap 

```

## Refs:

  * https://docs.percona.com/percona-software-repositories/installing.html
