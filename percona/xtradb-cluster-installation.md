# Installation percona xtradb cluster (5.7.) 

## Install Repo 

```
# as root 
apt update 
apt install curl
curl -O https://repo.percona.com/apt/percona-release_latest.generic_all.deb

apt install gnupg2 lsb-release ./percona-release_latest.generic_all.deb
apt update
```




## Refs:

  * https://docs.percona.com/percona-software-repositories/installing.html
