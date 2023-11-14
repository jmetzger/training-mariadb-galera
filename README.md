# MariaDB - Administration / Galera - Training (Redhat/Centos/Ubuntu/Debian)

## Agenda 

  1. Technical Background / Basics  
     * [Cluster Aufbau](cluster-structure.md)
     * [Technical Structure](technical-structure.md)
     * [Benefits of galera](benefits-galera.md) 
     * [Used Ports](arch.md)
     * [GTID - galera](galera-gtid.md)
     * [State Changes - SST/IST](sst-ist.md)
 
  1. Installation
     * [Installation (Debian 12)](installation-debian-12.md)
  
  1. Configuration / Evaluation 
     * [MariaDB Cluster Configuration (Debian 11/12 / MariaDb 10.06/10.11)](cluster-configuration-debian-11.md)
     * [Is Cluster running?](is-cluster-running.md)
     * [wsrep_ vars and wsrep_provider_options for config](wsrep-config.md) 
     * [Adjusted wsrep_node_address for multiple interfaces scenario](using-wsrep-node-address.md)
     * [Setting up the firewall with firewalld](firewalld.md)
   
  1. Troubleshooting  
     * [Handling on power outage](galera-poweroutage.md) 
     * [Node not coming up -start fresh](troubleshoot-galera-node.md)
     * [Find slow nodes](find-slow-nodes.md)
     
  1. MaxScale 
     * [License MaxScale](maxscale-license.md)
     * [Installation/Configuration - Debian/Ubuntu](maxscale-installation-configuration-debian-ubuntu.md) 
     * [MaxScale Torubleshooting](maxscale-troubleshooting.md)
     * [Monitoring MaxScale](https://mariadb.com/kb/en/mariadb-maxscale-24-mariadb-maxscale-nagios-plugins-for-nagios-351/)
  
  1. ProxySQL 
     * [Proxysql mit Galera aufsetzen](proxysql.md)
     * [Sharding mit ProxySQL](proxysql-sharding.md)
  
  1. Monitoring 
     * [Monitoring Galera Cluster](monitoring.md) 
     
  1. Maintenance/Administration 
     * [Upgrading major version](galera-upgrade-major.md)
     * [Schema Upgrades](schema-upgrades.md)
     * [Find good gcache-size](determine-gcache.md) 
     * [Create fresh datadir](create-fresh-datadir.md)
 
  1. Performance Optimization 
     * [Performance Tracking/Optimization Galera](performance-galera.md) 

  1. Backup und Restore
     * [Backup und Restore with Mariabackup (Ubuntu/Debian)](galera-mariabackup.md)
     * [Backup und Recover - mysqldump/mysql ](mysqldump-mysql.md) 
 
  1. MySQL - Galera - Cluster
     * [Overall Document](Galera.md#10-mysql-galera-cluster)
  
  1. Deploy Galera Cluster + Maxscale with Ansible 
     * [Deployment-github-repo](https://github.com/jmetzger/ansible-galera-cluster-maxscale/blob/master/README.md)
 
  1. Security 
     * [Encryption galera cache](encryption-galera.md)
     * [Attacking Cluster -- adding node](add-other-node-insecure.md)
     * [Galera ssl-encryption](galera-ssl.md)
     
  1. Misc 
     * [EasyPeasy - mysqladmin](mysqladmin.md) 
     * [Backup with Arbitrator aka garbd](garbd-backup.md)
     * [Geolocation galera](galera-geolocation.md)

  1. Dokumentation (Releases)
     * [Identify Long-Term Support Releases](https://mariadb.com/kb/en/mariadb-server-release-dates/) 
     
  1. Documentation 
     * [galera cluster - documentation parameters](https://galeracluster.com/library/documentation/galera-parameters.html)
     * [thread pool](https://mariadb.com/kb/en/thread-pool-in-mariadb/)
     * [Documentation/Help](documentation-help.md)

## Backlog  

  1. Installation 
     * [Installation (Ubuntu 20.04)](installation-ubuntu-20-04.md)
     * [Installation (Centos 8)](installation-centos-8.md)

  1. Configuration
     * [MariaDB Cluster Configuration (Ubuntu 20.04 LTS / MariaDb 10.04)](cluster-configuration-ubuntu-20-04.md)
     * [MariaDB Cluster Configuration (Centos)](cluster-configuration-centos.md) 
 
  
   
### 13 Performance 

```
innodb_flush_log_at_trx_commit = 2
# up to 75x faster 
https://dba.stackexchange.com/questions/12611/is-it-safe-to-use-innodb-flush-log-at-trx-commit-2/56673#56673

innodb_flush_method = O_DIRECT_NO_FSYNC
# Not suitable for XFS filesystems.

# recommendation from galera 
innodb_autoinc_lock_mode = 2 

```







