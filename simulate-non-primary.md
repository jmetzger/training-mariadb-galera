# Simulate non-primary 

## Scenario 1: 2 node cluster (please not for production, only for simulation here) 

```
#!/bin/bash
# block_galera.sh
# galera2, 192.168.55.172

iptables -I INPUT -m tcp -p tcp --dport 4567 -j REJECT
iptables -I INPUT -m tcp -p tcp --dport 3306 -j REJECT
iptables -I OUTPUT -m tcp -p tcp --dport 4567 -j REJECT
iptables -I OUTPUT -m tcp -p tcp --dport 3306 -j REJECT
# print timestamp
date

```
