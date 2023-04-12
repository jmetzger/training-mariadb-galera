# Start Cluster after poweroutage  

## Prerequisites 

  * Service mariadb was not enabled for automatic restart
  * Check: systemctl is-enabled mariadb 
 
```
systemctl is-enabled mariadb
disabled
```

## Schritte:

  * Execute this on EVERY Node.
  
```
## Imporant galera should not run on any node: mariadb server darf nicht laufen 
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
