# When and when not to use galera

## When does a cluster make sense and when not ?

### PRE-Requisite

*  The primary focus of Galera Cluster is data consistency across the nodes.

#####  DOES NOT: Application not ready

*  If application has a lot of hotspots, writing to all nodes will be critical
*  e.g. update counter set counter=counter+1; # Table counter having just one row
*  This will produce a lot of "DEADLOCKS".

##### DOES NOT: InnoDB is not used as engine

*  For MariaDB Cluster to work properly InnoDB-Engine is needed
*  MyISAM is currently experimental.. does not support DML's (e.g. INSERT into ....)

#####  DOES NOT: Windows is used

*  MariaDB Cluster does not work on Windows

##### DOES NOT: Application heavily uses Query_Cache

*  If you heavily rely on Query Cache you should first \\ fix the queries and redesign your application. \\ There is experimental Query Cache support since Galera v3.1. \\ But the Galera documentation clearly states: Do not use query cache..

#####  Refs


*  https://www.fromdual.com/limitations-of-galera-cluster
