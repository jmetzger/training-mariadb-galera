# Installation 

## Get repo and install (Ubuntu 22.04 LTS) 

```
# https://mariadb.org/download/?t=repo-config&d=22.04+%22jammy%22&v=10.6&r_m=agdsn
sudo apt-get install apt-transport-https curl
sudo curl -o /etc/apt/trusted.gpg.d/mariadb_release_signing_key.asc 'https://mariadb.org/mariadb_release_signing_key.asc'
sudo sh -c "echo 'deb https://ftp.agdsn.de/pub/mirrors/mariadb/repo/10.6/ubuntu jammy main' >>/etc/apt/sources.list"

sudo apt update
sudo apt install mariadb-server 
hostnamectl set-hostname galera1.training.local 
```

## Only when working with virtual box 

### Steps 

  * Maschinen 2x in virtualbox clonen 

###  Important delete -  machine-id 

  *  Otherwice the machines will have the same ip 

```
sudo rm -f /etc/machine-id 
sudo systemd-machine-id-setup
sudo hostnamectl set-hostname galera2.training.local
sudo reboot 
```

  
