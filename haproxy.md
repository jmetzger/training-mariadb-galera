# haproxy (Load Balancing) 

## Walkthrough 

```
apt install -y haproxy 
```

```
# add the following lines /etc/haproxy/haproxy.cfg 
# Load Balancing for Galera Cluster
listen galera
     bind :3306 
     balance source
     mode tcp
     option tcpka
     option mysql-check user haproxy
     server node1 192.168.1.1:3306 check weight 1
     server node2 192.168.1.2:3306 check weight 1
     server node3 192.168.1.3:3306 check weight 1

```

```
systemctl restart haproxy 
```

```
# Create a user for connecting on one of the nodes 
create user 'ext'@'%' identified by '11berlin22';
grant all on *.* to ext@'%' ;
```


```
# Try connection from external server 
mysql -u ext -p -h <public-ip-of-haproxy>
select @@hostname 

```

```
# Stop node 3 and see how logs changes 
# on node 3
systemctl stop mariadb 

# on haproxy 
tail /var/log/haproxy.log 

# try to reconnect now and see how it looks like 
# Try connection from external server 
mysql -u ext -p -h <public-ip-of-haproxy>
select @@hostname 
```

## Reference:

  * https://cyral.com/blog/how-to-galera-mariadb-haproxy/
  * https://galeracluster.com/library/documentation/ha-proxy.html
