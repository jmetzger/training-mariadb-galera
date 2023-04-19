# Installation

## Get repo and install (Debian 11 and MariaDB 10.6) 

```
# https://mariadb.org/download/?t=repo-config&d=Debian+11+%22Bullseye%22&v=10.6&r_m=agdsn
sudo apt-get install apt-transport-https curl
sudo curl -o /etc/apt/trusted.gpg.d/mariadb_release_signing_key.asc 'https://mariadb.org/mariadb_release_signing_key.asc'
sudo sh -c "echo 'deb https://ftp.agdsn.de/pub/mirrors/mariadb/repo/10.6/debian bullseye main' >>/etc/apt/sources.list"


sudo apt update
sudo apt install -y mariadb-server 
# hostnamectl set-hostname g1.t3isp.de 
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

  
