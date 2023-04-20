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
# desync = off 
mysql -e "set global wsrep_desync='off'"


# prepare 
mariabackup --target-dir=/backups/20230420 --galera-info --prepare 

```

## Walkthrough (Recovery for node to join to cluster) but with backup 

```
# Step 1:
Reset the server 
systemctl stop mariadb 
mv /var/lib/mysql /var/lib/mysql.old 
# prepare grstate.dat 
cd /backups/20230420
vi grastate.dat 
```

```
# You need to adjust the gtid (seq-no)
```

```
# GALERA saved state
version: 2.1
uuid:    <enter from xtrabackup_galera_info uuid here>
seqno:   <enter seq_no (value behind :) from xtrabackup_galer_info here>
safe_to_bootstrap: 0
```

```
mariabackup --target-dir=/backups/20230420 --copy-back 
chown -R mysql:mysql /var/lib/mysql
systemctl start mariadb 
```

```
# is it part of the cluster again 
show status like '%wsrep%';
```
