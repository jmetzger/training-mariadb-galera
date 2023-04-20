# Setup firewall rules with firewall-cmd for galera 

## Prerequisites (on all nodes)

```
# is firewalld installed ?
systemctl status firewalld 

# How to the rules look like ? 
firewall-cmd --list-all 

# installing if not there 
apt install -y firewalld
systemctl status firwalld 
systemctl enable firewalld
firwall-cmd --list-all 
```


## Step 1: (only for Debian / Ubuntu) 

```
sudo firewall-cmd --permanent --zone=public --add-interface={eth0,eth1}
sudo firewall-cmd --reload 
```

## Step 2: Walkthrough (on all nodes) 
```
sudo firewall-cmd --permanent --add-port={3306,4444,4567,4568}/tcp
sudo firewall-cmd --permanent --add-port=4567/udp
sudo firewall-cmd --reload
```

## Step 3: Evaluate 

```
# on one node 
show status like 'wsrep%size';
```

