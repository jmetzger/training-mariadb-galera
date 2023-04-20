# Setup firewall rules with firewall-cmd for galera 

## Prerequisites 

```
# is firewalld installed ?
systemctl status firewalld 

# How to the rules look like ? 
firewall-cmd --list-all 
```

## Walkthrough 
```
sudo firewall-cmd --permanent --add-port={3306,4444,4567,4568}/tcp
sudo firewall-cmd --permanent --add-port=4567/udp
sudo firewall-cmd --reload
```