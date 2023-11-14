# Installation

## Get information about installation of repo 

  * https://mariadb.org/download/?t=mariadb&p=mariadb&r=11.3.0
  * https://mariadb.org/download/?t=repo-config&d=Debian+12+%22Bookworm%22&v=10.11&r_m=agdsn


## Get repo and install (Debian 12 and MariaDB 10.11) 

```
sudo apt-get install apt-transport-https curl
sudo mkdir -p /etc/apt/keyrings
sudo curl -o /etc/apt/keyrings/mariadb-keyring.pgp 'https://mariadb.org/mariadb_release_signing_key.pgp'
```

```
nano /etc/apt/sources.list.d/mariadb.sources
```

```
# MariaDB 10.11 repository list - created 2023-11-14 10:43 UTC
# https://mariadb.org/download/
X-Repolib-Name: MariaDB
Types: deb
# deb.mariadb.org is a dynamic mirror if your preferred mirror goes offline. See https://mariadb.org/mirrorbits/ for details.
# URIs: https://deb.mariadb.org/10.11/debian
URIs: https://ftp.agdsn.de/pub/mirrors/mariadb/repo/10.11/debian
Suites: bookworm
Components: main
Signed-By: /etc/apt/keyrings/mariadb-keyring.pgp
```

```
sudo apt-get update
# mariadb-backup if using mariadbackup as sst, no problem if installed anyways 
sudo apt-get install mariadb-server mariadb-backup 
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

  
