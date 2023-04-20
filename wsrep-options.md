# Where to set wsrep_ setting

## Normally specific galera wsrep - vars 

```
show variables like 'wsrep%';
# in most cases you wil set these in the config file of your installation 
```

## For specific settings you might need to set them in .... 

```
# in config as well 
wsrep_provider_options 

```

## Reference:

  * https://galeracluster.com/library/documentation/galera-parameters.html
  * https://mariadb.com/kb/en/wsrep_provider_options/
  * https://mariadb.com/kb/en/galera-cluster-system-variables/
