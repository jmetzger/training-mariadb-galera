# Proxysql 

## Important Reference:

  * https://proxysql.com/blog/proxysql-native-galera-support/

![Proxysql galera](https://proxysql.com/wp-content/uploads/2019/03/galera_cluster_native_support_in_proxysql.png)


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
select * from mysql_galera_hostgroups;
```

### Step 3: Configuration in die runtime laden

```
LOAD MYSQL SERVERS TO RUNTIME;
SAVE MYSQL SERVERS TO DISK;
```

### Step 4: Verify connections 

```
--  server with lowest weight moved to hostgroup -> 4 (backup_writer) from 2
select hostgroup,srv_host,status,ConnUsed,MaxConnUsed,Queries,Latency_us from stats.stats_mysql_connection_pool order by srv_host;
```

### Step 5: Define SQL Query Rules 

```
-- do this in Admin> interface 
INSERT INTO mysql_query_rules (active, match_digest, destination_hostgroup, apply) VALUES (1, '^SELECT.*',3, 0);
INSERT INTO mysql_query_rules (active, match_digest, destination_hostgroup, apply) VALUES (1, '^SELECT.* FOR UPDATE',2, 1);
LOAD MYSQL QUERY RULES TO RUNTIME;
SAVE MYSQL QUERY RULES TO DISK;

```

### Step 5.5 On galera node (used as readonly -> hostgroup 3 -> 10.135.0.15 (refer to Step 2) set read_only 

```
mysql -e "set global read_only=1"
```

### Step 6: Show logs from galera 

```
select * from mysql_server_galera_log order by time_start_us desc limit 3;
```

### Step 7: ON PROXY-SQL 

```
-- In Admin -> interface 
select hostgroup,srv_host,status,ConnUsed,MaxConnUsed,Queries,Latency_us from stats.stats_mysql_connection_pool order by srv_host;
```

```
# nur Ausgabe 2 + Online = Writer  
+-----------+-------------+---------+----------+-------------+---------+------------+
| hostgroup | srv_host    | status  | ConnUsed | MaxConnUsed | Queries | Latency_us |
+-----------+-------------+---------+----------+-------------+---------+------------+
| 3         | 10.135.0.15 | ONLINE  | 0        | 0           | 0       | 900        |
| 2         | 10.135.0.4  | ONLINE  | 0        | 0           | 0       | 1021       |
| 2         | 10.135.0.9  | SHUNNED | 0        | 0           | 0       | 1041       |
| 4         | 10.135.0.9  | ONLINE  | 0        | 0           | 0       | 1041       |
+-----------+-------------+---------+----------+-------------+---------+------------+
4 rows in set (0.003 sec)
```

## Step 8: On current writer: hostgroup 2 + ONLINE -> 10.135.0.4 (Server 1 in my case) 

```
-- on 10.135.0.4
-- mysql
set global wsrep_desync = 1;
```

## Step 9: ON - PROXY SQL 

```
select hostgroup,srv_host,status,ConnUsed,MaxConnUsed,Queries,Latency_us from stats.stats_mysql_connection_pool order by srv_host;
```

```
+-----------+-------------+--------------+----------+-------------+---------+------------+
| hostgroup | srv_host    | status       | ConnUsed | MaxConnUsed | Queries | Latency_us |
+-----------+-------------+--------------+----------+-------------+---------+------------+
| 3         | 10.135.0.15 | ONLINE       | 0        | 0           | 0       | 787        |
| 2         | 10.135.0.4  | OFFLINE_HARD | 0        | 0           | 0       | 975        |
| 1         | 10.135.0.4  | ONLINE       | 0        | 0           | 0       | 975        |
| 2         | 10.135.0.9  | ONLINE       | 0        | 0           | 0       | 912        |
| 4         | 10.135.0.9  | OFFLINE_HARD | 0        | 0           | 0       | 912        |
+-----------+-------------+--------------+----------+-------------+---------+------------+
```

## Experiment with system 

### Step 1: Update max_writers 

```
update mysql_galera_hostgroups set max_writers=2;
select hostgroup_id, hostname, port, gtid_port, status, weight from runtime_mysql_servers;
-- Achtung, abweichend von doku proxysql -> so funktionierts 
LOAD MYSQL SERVERS TO RUNTIME;
SAVE MYSQL SERVERS TO DISK;
```

### Step 2: Setting writer_is_also_reader to 0 (with max_writers set back to 1).

```
update mysql_galera_hostgroups set max_writers=1,writer_is_also_reader=0;
select hostgroup_id, hostname, port, gtid_port, status, weight from runtime_mysql_servers;
LOAD MYSQL SERVERS TO RUNTIME;
SAVE MYSQL SERVERS TO DISK;
```

## Nutzer zum Verwenden in galera cluster anlegen 

### Schritt 1: Auf einer Nodes des Galera Clusters 

```
-- auf einem nodes nutzer anlegen
-- mysql>
-- ES IST ausreichend, dass der proxysql - server zugreifen kann 
create user extern@10.135.0.18 identified by 'password';
grant all on sakila.* to extern@10.135.0.18
```

### Schritt 2: Nutzer in proxysql anlegen 

```
-- im Admin - Interface
-- Admin>
INSERT INTO mysql_users(username,password,default_hostgroup) VALUES ('extern','password',2);
LOAD MYSQL USERS TO RUNTIME;
SAVE MYSQL USERS TO DISK;
```

```
# Externe IP-Adresse des Proxy-SQL-Server rausfinden
ip -br -c a
```

### Schritt 3: Von entferntem System ausserhalb von proxysql und cluster 

```
mysql -u extern -ppassword -h 165.232.69.248 -P6033 -e"insert into actor (first_name,last_name) values ('hans','hansi');" sakila
```


## References

  * https://proxysql.com/blog/proxysql-native-galera-support/
  * https://proxysql.com/blog/effortlessly-scaling-out-galera-cluster-with-proxysql/
  * https://severalnines.com/database-blog/how-run-and-configure-proxysql-20-mysql-galera-cluster-docker
