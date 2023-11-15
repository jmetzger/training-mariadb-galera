# Proxysql 

## Installation unter Debian 12 

```
# DO THIS AS ROOT ! 
apt-get install -y --no-install-recommends lsb-release wget apt-transport-https ca-certificates gnupg
wget -nv -O /etc/apt/trusted.gpg.d/proxysql-2.5.x-keyring.gpg 'https://repo.proxysql.com/ProxySQL/proxysql-2.5.x/repo_pub_key.gpg'
echo deb https://repo.proxysql.com/ProxySQL/proxysql-2.5.x/$(lsb_release -sc)/ ./ | tee /etc/apt/sources.list.d/proxysql.list
# Referenz:
# https://proxysql.com/documentation/installing-proxysql/
apt update
# Client needed to access admin - databases from proxysql 
apt install -y proxysql mariadb-client 
```

## Reinitialize from config-file (after first startup database is used)

```
# config-file /etc/proxysql.conf 
systemctl start proxysql-initial
```

## Configuration through admin-interface 

```
mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt='Admin> '
```

```
-- mysql
show databases;
select * from global_variables where variable_name like '%monitor_galera%';

-- there is a specific hostgroup for galera
SHOW CREATE TABLE mysql_galera_hostgroups;


```

### What is what in the mysql_galera_hostgroups 

  * See meaning of fields in https://proxysql.com/documentation/galera-configuration/

## Walkthrough - Setting up proxysql with galera 

  * https://proxysql.com/blog/effortlessly-scaling-out-galera-cluster-with-proxysql/


### Step 1: Setting up servers to monitor and to access 

```
mysql -P6032 -uadmin -padmin -h 127.0.0.1
```

```
### Remark 
#writer_hostgroup=0, reader_hostgroup=1, backup_writer_hostgroup=2, offline_hostgroup=4. The node with the lowest weight will get configured into #backup_writer_hostgroup after loading the configuration into runtime, if all servers share the default weight=1 then a random server is selected as is the case in #our configuration:
```

```
-- mysql>
DELETE FROM mysql_servers;
INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (0,'10.135.0.4',3306);
INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (0,'10.135.0.9',3306);
INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (1,'10.135.0.15',3306);
LOAD MYSQL SERVERS TO RUNTIME;
```





## References

  * https://proxysql.com/blog/proxysql-native-galera-support/
  * https://severalnines.com/database-blog/how-run-and-configure-proxysql-20-mysql-galera-cluster-docker
