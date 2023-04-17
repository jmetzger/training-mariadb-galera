# MariaDB - Administration / Galera - Training (Redhat/Centos/Ubuntu/Debian)

## Agenda 

  1. Technical Background / Basics  
     * [Technical Structure](technical-structure.md)
     * [Used Ports](arch.md)
 
  1. Installation
     * [Installation (Debian 11)](installation-debian-11.md)
  
  1. Configuration
     * [MariaDB Cluster Configuration (Ubuntu 20.04 LTS / MariaDb 10.04)](cluster-configuration-ubuntu-20-04.md)
     * [MariaDB Cluster Configuration (Centos)](cluster-configuration-centos.md) 
  
  1. Troubleshooting  
     * [Handling on power outage](galera-poweroutage.md) 
  
  1. Tipps & Tricks 
     * [Find good gcache-size](determine-gcache.md) 
  
  1. MaxScale 
     * [License MaxScale](maxscale-license.md)
     * [Installation/Configuration - Debian/Ubuntu](maxscale-installation-configuration-debian-ubuntu.md) 
     * [MaxScale Torubleshooting](maxscale-troubleshooting.md)
  
  1. ProxySQL 
     * [Proxysql mit Galera aufsetzen](proxysql.md)
     * [Sharding mit ProxySQL](proxysql-sharding.md)
  
  1. Monitoring 
     * [Monitoring Galera Cluster](monitoring.md) 

  1. Performance Optimization 
     * [Performance Tracking/Optimization Galera](performance-galera.md) 

  1. Backup und Restore
     * [Backup und Restore with Mariabackup (Ubuntu/Debian)](backup-mariabackup-ubuntu-debian.md)
     * [Backup automatisiert](backup-automatisiert.md) 

   1. Documentation/Help
      * [Documentation-Server-System-Variables](#12-Documentation) 
      * [Documentation/Help](documentation-help.md)

  1. MySQL - Galera - Cluster
     * [Overall Document](Galera.md#10-mysql-galera-cluster)
  
  1. Deploy Galera Cluster + Maxscale with Ansible 
     * [Deployment-github-repo](https://github.com/jmetzger/ansible-galera-cluster-maxscale/blob/master/README.md)
  
  1. [Misc](#11-misc) 
 
  1. [Schema Upgrades](schema-upgrades.md)
  
  1. [Backup with Arbitrator aka garbd](garbd-backup.md)
  
  1. [Backup und Recover - mysqldump/mysql ](mysqldump-mysql.md) 
   
  1. [EasyPeasy - mysqladmin](mysqladmin.md) 

## Backlog  

  1. Installation 
     * [Installation (Ubuntu 20.04)](installation-ubuntu-20-04.md)
     * [Installation (Centos 8)](installation-centos-8.md)

  1. Configuration
     * [MariaDB Cluster Configuration (Ubuntu 20.04 LTS / MariaDb 10.04)](cluster-configuration-ubuntu-20-04.md)
     * [MariaDB Cluster Configuration (Centos)](cluster-configuration-centos.md) 
 

#### Server System Variables 

 * https://mariadb.com/kb/en/server-system-variables/

```
show variables like 'wsrep%' \G
show status like 'wsrep%' \G
```

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

### 14 Simulations 

  * Scenario, where node goes into Non-Primary mode, because the other
  * node have no connection 
  
```
## try this instead + do it on terminal !
## on node 3 (or another node) 
systemctl stop firewalld 
for i in  3306 4444 4567 4568 ; do firewall-cmd --remove-port=$i/tcp; done
iptables -A INPUT -p tcp --destination-port 80 -j DROP
```





