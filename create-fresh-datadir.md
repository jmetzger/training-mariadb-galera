# Create fresh datadir 

## Walkthrough (Centos/RHEL/Rocky) 

```
# Schritt 1: Prepare 
systemctl stop mariadb 
cd /var/lib 
# eventually delete old back dir
rm -fR /var/lib/mysql.bkup 
# 
mv mysql mysql.bkup 

# Schritt 2: Fresh 
mysql_install_db --user=mysql
chown mysql:mysql mysql
chmod g+rx,o+rx mysql 
restorecon -rv /var/lib/mysql 

# Schritt 3: Start 
systemctl start mariadb
```

## Walkthrough (Debian/Ubuntu) 

```
# Schritt 1: Prepare 
systemctl stop mariadb 
cd /var/lib 
# eventually delete old back dir
rm -fR /var/lib/mysql.bkup 
# 
mv mysql mysql.bkup 

# Schritt 2: Fresh 
mysql_install_db --user=mysql

# not sure, but safe ! 
chown mysql:mysql mysql
chmod g+rx,o+rx mysql 

# Schritt 3: Start 
systemctl start mariadb
```
