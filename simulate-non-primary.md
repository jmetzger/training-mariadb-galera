# Simulate non-primary 

## Scenario 1: 3 node cluster


### Step 1: Execute firewall rules on node 1

```
# on 1 node: e.g. node1 execute the following firewall rules 
```


```
iptables -I INPUT -m tcp -p tcp --dport 4567 -j REJECT
iptables -I INPUT -m tcp -p tcp --dport 3306 -j REJECT
iptables -I OUTPUT -m tcp -p tcp --dport 4567 -j REJECT
iptables -I OUTPUT -m tcp -p tcp --dport 3306 -j REJECT
# print timestamp
date

```

### Step 2: Obey what is happening

```
# Now have a look into node 1 
show status like 'wsrep_cluster_size';
show status like 'wsrep_cluster_status';

# Now have a look into node 2 + node 3 
show status like 'wsrep_cluster_size';
show status like 'wsrep_cluster_status';

```

### Step 3: Flush firewall rules (delete them) 

```
iptables -F 
```

### Step 4: Obey what is happening

```
# Now have a look into node 1 
show status like 'wsrep_cluster_size';
show status like 'wsrep_cluster_status';

# Now have a look into node 2 + node 3 
show status like 'wsrep_cluster_size';
show status like 'wsrep_cluster_status';

```





## Scenario 2: 3 node cluster (please not for production, only for simulation here) 
