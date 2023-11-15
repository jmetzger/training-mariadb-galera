# Proxysql 

## Important Reference:

  * https://proxysql.com/blog/proxysql-native-galera-support/


## Setting up the monitoring user in galera cluster on 1 of the nodes 

```
-- 
-- mysql>
CREATE USER 'monitor'@'%' IDENTIFIED BY 'monitor';
```


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

### Step 3: Setup the correct linking between hostgroups

```
We continue adding configuration that defines the behavior we want ProxySQL to have for our Galera cluster workload. For this we make use of the table mysql_galera_hostgroups. Initially we are going to set max_writers=1 and writer_is_also_reader=1. This means that we will have 1 writer and 3 readers, reader nodes will also be placed in the backup_writer_hostgroup. This is because:

As doc states “writer_is_also_reader=1: nodes in writer_hostgroup and backup_writer_hostgroup are also in reader hostgroup.”
The number of writers is limited to 1.
```

```
DELETE FROM mysql_galera_hostgroups;

INSERT INTO mysql_galera_hostgroups (writer_hostgroup, backup_writer_hostgroup, reader_hostgroup, offline_hostgroup, active, max_writers, writer_is_also_reader, max_transactions_behind, comment) VALUES (0, 2, 1, 4, 1, 1, 1, 100, NULL);

LOAD MYSQL SERVERS TO RUNTIME;
```

### Step 4: Verify tables 

```
select * from mysql_servers;
select * from mysql_galera_hostgroups\G
```

### Step 5: Load configuration and save it to disk 

```
LOAD MYSQL SERVERS TO RUNTIME;
-- will be saved to sqllite db under /var/lib/proxysql/proxysql.db
SAVE MYSQL SERVERS TO DISK;
```

## Look into monitoring 

### Step 1: 

```
mysql -P6032 -uadmin -padmin -h 127.0.0.1
```


```
SHOW TABLES FROM monitor;
## Dedicated logs for events
SELECT * FROM monitor.mysql_server_connect_log ORDER BY time_start_us DESC LIMIT 3;
SELECT * FROM monitor.mysql_server_ping_log ORDER BY time_start_us DESC LIMIT 3;
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
  * https://severalnines.com/database-blog/how-run-and-configure-proxysql-20-mysql-galera-cluster-docker
