# Proxysql 

## Installation unter Debian 12 

```
# DO THIS AS ROOT ! 
apt-get install -y --no-install-recommends lsb-release wget apt-transport-https ca-certificates gnupg
wget -nv -O /etc/apt/trusted.gpg.d/proxysql-2.5.x-keyring.gpg 'https://repo.proxysql.com/ProxySQL/proxysql-2.5.x/repo_pub_key.gpg'
echo deb https://repo.proxysql.com/ProxySQL/proxysql-2.5.x/$(lsb_release -sc)/ ./ | tee /etc/apt/sources.list.d/proxysql.list
# Referenz:
# https://proxysql.com/documentation/installing-proxysql/
apt update
apt install -y proxysql
```

## Reinitialize from config-file (after first startup database is used)

```
# config-file /etc/proxysql.conf 
systemctl start proxysql-initial
```





## References

  * https://proxysql.com/blog/proxysql-native-galera-support/
  * https://severalnines.com/database-blog/how-run-and-configure-proxysql-20-mysql-galera-cluster-docker
