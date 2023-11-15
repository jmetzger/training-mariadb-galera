# Proxysql 

## Important Reference:

  * https://proxysql.com/blog/proxysql-native-galera-support/

![https://proxysql.com/wp-content/uploads/2019/03/galera_cluster_native_support_in_proxysql.png](ProxySQL Galera Setup)


## Setting up the monitoring user in galera cluster on 1 of the galera-nodes 

```
-- 
-- mysql>
CREATE USER 'monitor'@'%' IDENTIFIED BY 'monitor';
```


## On proxysql-server: Installation unter Debian 12 

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

```
# systemctl commands work from here
systemctl status proxysql
```

```
# start proxysql service
systemctl start proxysql
```


## On proxysql-server: Walkthrough - Setting up proxysql with galera 

### Step 1: Set Up the monitoring user

```
mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt='Admin> '
```

```
-- Admin>
UPDATE global_variables SET variable_value='monitor' WHERE variable_name='mysql-monitor_username';
UPDATE global_variables SET variable_value='monitor' WHERE variable_name='mysql-monitor_password';
```

### Step 2: Setting up servers to monitor and to access 

```
mysql -P6032 -uadmin -padmin -h 127.0.0.1
```

```
-- Admin>
-- Setup Servers to monitor and use 
INSERT INTO mysql_servers(hostgroup_id,hostname,port,weight) VALUES (2,'10.135.0.4',3306,100);
INSERT INTO mysql_servers(hostgroup_id,hostname,port,weight) VALUES (2,'10.135.0.9',3306,10);
INSERT INTO mysql_servers(hostgroup_id,hostname,port,weight) VALUES (3,'10.135.0.15',3306,100);
```

```
-- Setup what hostgroup does what ?
INSERT INTO mysql_galera_hostgroups (writer_hostgroup,backup_writer_hostgroup,reader_hostgroup,offline_hostgroup,active,max_writers,writer_is_also_reader,max_transactions_behind) 
VALUES (2,4,3,1,1,1,0,100);
```

```
-- Verify
select hostgroup_id,hostname,port,status,weight,max_connections from mysql_servers;
select hostgroup_id,hostname,port,status,weight,max_connections from mysql_servers;
```

### Step 3: Configuration in die runtime laden

```
LOAD MYSQL SERVERS TO RUNTIME;
SAVE MYSQL SERVERS TO DISK;
```


## Experiment with system 

### Step 1: Update max_writers 

```
update mysql_galera_hostgroups set max_writers=2;
select hostgroup_id, hostname, port, gtid_port, status, weight from runtime_mysql_servers;

```

### Step 2: Setting writer_is_also_reader to 0 (with max_writers set back to 1).

```
update mysql_galera_hostgroups set max_writers=1,writer_is_also_reader=0;
select hostgroup_id, hostname, port, gtid_port, status, weight from runtime_mysql_servers;

```



## References

  * https://proxysql.com/blog/proxysql-native-galera-support/
  * https://proxysql.com/blog/effortlessly-scaling-out-galera-cluster-with-proxysql/
  * https://severalnines.com/database-blog/how-run-and-configure-proxysql-20-mysql-galera-cluster-docker
