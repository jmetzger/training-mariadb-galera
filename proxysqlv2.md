# Proxysql 

## Important Reference:

  * https://proxysql.com/blog/proxysql-native-galera-support/


## Setting up the monitoring user in galera cluster on 1 of the galera-nodes 

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
