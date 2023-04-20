# Mariabackup (galera) 

## Walkthrough (Backup) 

```
mysql -e "set global wsrep_desync='on'" 
apt install -y mariadb-backup 

# which options to user 
mkdir /backups 

echo "[mariabackup]" >> /root/.my.cnf
echo "user=root" >> /root/.my.cnf 

mariabackup --target-dir=/backups/20230420 --galera-info --backup 

# prepare 
mariabackup --target-dir=/backups/20230420 --galera-info --backup  

```
