# Create fresh datadir 


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

# Schritt 3: Start 
systemctl start mariadb
```
