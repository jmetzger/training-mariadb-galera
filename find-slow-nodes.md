#  Find slow nodes

## Basics

*  The cluster is as slow as the slowest node !
*  Sometimes we need to identify the slowest node

## Walkthrough 

```
# Node sent a pause event to the cluster when it was behind 

SHOW STATUS LIKE 'wsrep_flow_control_sent'; 
# > 0 => Cannot apply writesets as fast as they are received

SHOW STATUS LIKE 'wsrep_local_recv_queue_avg';
+----------------------------+---------+
 | Variable_name | Value | 
 | ------------- | ----- | 
+----------------------------+---------+
 | wsrep_local_recv_queue_avg | 3.34852 | 
 | -------------------------- | ------- | 
+----------------------------+---------+
```

*  Ref: https://galeracluster.com/library/kb/detecting-slow-node.html
