# Galera geolocation 

```


Image segments
https://severalnines.com/sites/default/files/blog/node_5775/image2.png

Relaying:
https://severalnines.com/sites/default/files/blog/node_5775/image3.png

gmcast.segment 
(default 0) 

# other datacenter 2 -> 1 
gmcast.segment = 1 

# other datacenter 3 
gmcast.segment = 2

Let us setup a second datacenter. 
Using segments, the communication will only be done to 1 node
on the other side (other datacenter) 
When doing sst/ist a node will be chosen from the same datacenter
(Can we check this ?) 

Ideally we should have 3 datacenter, so in case of an outage, the other 
2 know, that they are the majority.
```

## Documentation 

  * https://galeracluster.com/2015/07/geo-distributed-database-clusters-with-galera/
  * https://galeracluster.com/2015/07/cant-be-any-faster-than-that-a-real-life-experiment-with-latency-in-a-geo-distributed-environment/
