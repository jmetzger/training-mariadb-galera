# Major Galera Upgrade (e.g. 10.6. - 10.11) 

## Walkthrough 

```
# 1. Adjust repo in /etc/apt/sources.list or /etc/apt/sources.list.d/mariadb.list 
# or /etc/sources.list.d/mariadb.sources, depending what you have used 
deb https://ftp.agdsn.de/pub/mirrors/mariadb/repo/10.11/debian bullseye main
apt update 

# 2. Adjust load balancer not to send any traffic to node 

# 3. Stop the server 
systemctl stop mariadb 

# 4. uninstall 
apt remove -y mariadb-*
apt autoremove -y 

# 5. install new version 
apt install -y mariadb-server mariadb-backup 
systemctl start mariadb 

# 6. Evalatuate
show status like 'wsrep%';

# 7. Upgrade system tables
# normally this should already be done automatically on server startup 
# but executing it ones more does no harm 
mysql_upgrade --skip-write-binlog 

```

## Reference:

  * https://mariadb.com/kb/en/upgrading-from-mariadb-103-to-mariadb-104-with-galera-cluster/
