# Start Cluster after power outage  

## Produce power outage - exercise (simulate power outage)

```
# on all nodes 
ps aux | grep mariadbd 
# e.g. 5911 
```

```
kill -9 5911; systemctl stop mariadb 
```

## What is the catch ? 

  * You want to start the cluster with the most recent node 

## Prerequisites 

  * Service mariadb was not enabled for automatic restart
  * Check: systemctl is-enabled mariadb 
 
```
systemctl is-enabled mariadb
disabled
```

## Step 1: Identify first node to start

  * Execute this on EVERY Node.
  * Start node with highest seq_no firstly with galera_new_cluster
  
```
## Imporant mariadb should not run on any node 
systemctl is-active mariadb 
systemctl status mariadb 
galera_recovery 
```

```
# Eventually not needed because of galera_recovery 
sudo -u mysql mysqld --wsrep-recover
# In the last line you will have uid and the offset position (after :, here 21) 
2020-11-17 11:30:31 0 [Note] WSREP: Recovered position: 6dcdc984-2808-11eb-abeb-f799ea8dadff:21
```

## Step 2: Adjust grastate.dat in Server with highest seq_no 

```
# only if it is not safe_to_bootstrap on one the hosts 
# change safe to bootstrap from safe_to_bootstrap: 0 -> safe_to_bootstrap: 1
```

```
# on this node 
galera_new_cluster
```
