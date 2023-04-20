# Major Galera Upgrade (e.g. 10.6. - 10.11) 

## Walkthrough 

```
# 1. Adjust repo in /etc/sources.list 
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

```

## Reference:

  * https://mariadb.com/kb/en/upgrading-from-mariadb-103-to-mariadb-104-with-galera-cluster/
