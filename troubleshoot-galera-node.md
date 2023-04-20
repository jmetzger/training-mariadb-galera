# Troubleshoot galera node

## What happens ? 
  
  * Node is not coming up, we do not know why .... 

## How to proceed ? 

  * If all the other nodes are running, simple start from scratch 
  * If you are in the middle of a major update, this is the best route to go.

## Walkthrough 

```
# Get old datadir out of the way
mv /var/lib/mysql /var/lib/mysql.old-data

# Important that galera-config is properly setup 
# e.g. 
# wsrep_cluster_addresses 

# this creates all the system variables 
mysql_install_db --user=mysql 
chown -R mysql:mysql /var/lib/mysql 

# and you must not be the first node to start
systemctl start mariadb 

# now check, if you are part cluster again 
show status like 'wsrep%'; 


```
