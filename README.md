# good_to_know

# MySQL Backup and Restart Script

This Bash script is designed to perform backup operations for a MySQL/MariaDB server container and restart it. It includes a safety check to ensure that the specified branch name does not contain the character '/'.

## Prerequisites

- Docker installed on the host machine.
- `sudo` privileges to execute Docker and rsync commands.

## Usage

```bash
./backup_and_restart.sh [BRANCH]
```

##Maria DB save

```
#!/bin/bash

#cannot contain character /
#TODO: do check, fail if character present
BRANCH="${1:-default}"

rootDefaults=/home/root/mysql-root-defaults

sudo docker exec -i dev_mariadb mariadb --defaults-extra-file=$rootDefaults -e "purge binary logs before now()"

docker stop dev_mariadb

sudo rsync -var --progress --delete /speedy/mysql/ /data/tmp/mysql/$BRANCH/
sudo rsync -var --progress --delete /var/log/mysql/ /data/tmp/mysqllog/$BRANCH/

docker start dev_mariadb
```

##Docker compose start

```
#!/bin/bash

sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml up -d
```
##Docker compose stop
```
#!/bin/bash

sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml down
```
##Docker compose update
```
#!/bin/bash

sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml down

docker volume rm docker-dev-env_phpmyadmin_data

sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml pull
sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml build
sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml up -d
```
##Docker compose restart
```
#!/bin/bash

sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml down
sudo /usr/local/bin/docker-compose -f /speedy/config/docker-dev-env/docker-compose.yml up -d
```
##Maria DB restore
```
#!/bin/bash

#cannot contain character /
#TODO: do check, fail if character present
BRANCH="${1:-default}"

rootDefaults=/home/root/mysql-root-defaults

docker stop dev_mariadb

sudo rsync -var --progress --delete /data/tmp/mysql/$BRANCH/ /speedy/mysql/
sudo rsync -var --progress --delete /data/tmp/mysqllog/$BRANCH/ /var/log/mysql/

docker start dev_mariadb
```
