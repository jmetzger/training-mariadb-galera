# Using wsrep_node_address (Debian / Ubuntu ) 

## Why ? 

  * Having multiple network interfaces and not using the first one (e.g. eth0) 

## Version 1

## Step 1: Adjust configuration files on each node / restart and check incoming_address 

```
r1 - 10.135.0.7 
r11 - 10.135.0.14 
r111 - 10.135.0.21 

```

```
on r1 / etc/mysql/mariadb.conf.d/60-galera.cnf 
wsrep_node_address = 10.135.0.7 
systemctl restart mariadb 
mysql -e "show variables like '%incoming%'";

on r11 / 
wsrep_node_address = 10.135.0.14 
systemctl restart mariadb 
mysql -e "show variables like '%incoming%'";

on r111 / 
wsrep_node_address = 10.135.0.21
systemctl restart mariadb 
mysql -e "show variables like '%incoming%'";
```




## Version 2 (need to stop cluster) - not recommended 

## Step 1: Adjust configuration files on all node 

```
r1 - 10.135.0.7 
r11 - 10.135.0.14 
r111 - 10.135.0.21 

```

```
on r1 / etc/mysql/mariadb.conf.d/60-galera.cnf 
wsrep_node_address = 10.135.0.7 

on r11 / 
wsrep_node_address = 10.135.0.14 

on r111 / 
wsrep_node_address = 10.135.0.21
```

## Step 2: Restart the cluster 

```
# on each node.. r1, r11, r111 
systemctl stop mariadb 
```

```
# starting with last stopped node 
# safe_to_bootstrap: 1 # in /var/lib/mysql/grastate.dat
galera_new_cluster

# on the other nodes 
systemctl start mariadb 

```

## Step 3: Evalulate incoming_addresses

```
# on one of the nodes 
show status like 'wsrep%incoming%';
```
