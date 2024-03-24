# jk13xyz/wavelog

This is an unofficial Docker image for [Wavelog](https://github.com/wavelog/wavelog), a PHP based amateur radio logging software and a fork of [Cloudlog](https://github.com/magicbug/cloudlog) by 2M0SQL. The underlying Dockerfile is a fork and rewrite of [int2001/wavelog_docker](https://github.com/int2001/wavelog_docker), adding additional features (see "Config").

This image is for personal use only and comes without support. If you'd like support, please use int2001's version, as this will become the official Wavelog image for Docker.

## Config

- Based on php:8.3-apache,
- mod_rewrite enabled,
- No "/index.php/" in the URL,
- Comes with (most) cronjobs out-of-the-box.

## Current version

1.3.1

**Please note**: The internal "Update" function has not been tested".

## Usage

**IMPORTANT: MAKE SURE TO CHANGE THE MARIADB PASSWORD!**

### Docker Compose

```yaml
version: '3'
services:
  wavelog-main:
    image: jk13xyz/wavelog:latest
    container_name: wavelog-main
    depends_on:
      - wavelog-mariadb
    volumes:
      - wavelog-config:/var/www/html/application/config
      - wavelog-uploads:/var/www/html/uploads
      - wavelog-images:/var/www/html/images
    ports:
      - "7373:80"
    restart: unless-stopped  
    
  wavelog-mariadb:
    image: mariadb:latest
    container_name: wavelog-mariadb
    environment:
      MARIADB_RANDOM_ROOT_PASSWORD: yes
      MARIADB_DATABASE: wavelog
      MARIADB_USER: wavelog
      MARIADB_PASSWORD: "STRONG_PASSWORD"
    volumes:
      - wavelog-dbdata:/var/lib/mysql
    restart: unless-stopped

  wavelog-phpmyadmin:
    image: phpmyadmin:latest
    container_name: wavelog-phpmyadmin
    depends_on:
      - wavelog-mariadb
    environment:
      UPLOAD_LIMIT: 128M    
      PMA_HOST: wavelog-mariadb
      PMA_PORT: 3306
      PMA_USER: wavelog
      PMA_PASSWORD: "STRONG_PASSWORD"
    restart: unless-stopped
    ports:
      - "7374:80" 

volumes:
  wavelog-dbdata:
  wavelog-config:
  wavelog-uploads:
  wavelog-images:
```

### Docker Run

```bash
docker volume create wavelog-dbdata && \
docker volume create wavelog-config && \
docker volume create wavelog-images && \
docker volume create wavelog-uploads
    
docker run -d \
    --name wavelog-main \
    -v wavelog-config:/var/www/html/application/config \
    -v wavelog-backup:/var/www/html/application/backup \
    -v wavelog-images:/var/www/html/images \    
    -v wavelog-uploads:/var/www/html/uploads \
    -v wavelog-crontab:/var/www/html/crontab \
    -p 7373:80 \
    --restart unless-stopped \
    jk13xyz/wavelog:latest

docker run -d \
    --name wavelog-mariadb \
    -e MARIADB_RANDOM_ROOT_PASSWORD=yes \
    -e MARIADB_DATABASE=wavelog \
    -e MARIADB_USER=wavelog \
    -e MARIADB_PASSWORD="STRONG_PASSWORD" \
    -v wavelog-dbdata:/var/lib/mysql \
    --restart unless-stopped \
    mariadb:latest

docker run -d \
    --name wavelog-phpmyadmin \
    -e PMA_HOST=wavelog-mariadb \
    -e PMA_PORT=3306 \
    -e PMA_USER=wavelog \
    -e PMA_PASSWORD="STRONG_PASSWORD" \
    --restart unless-stopped \
    -p 7374:80 \
    phpmyadmin:latest
```

## Install

1. Open Wavelog on your host (e.g. localhost:7373).
2. Change the Locator and adjust the URL, if necessary. You can leave "Directory" usually empty.
3. Enter the database credentials

    - Host: wavelog-mariadb
    - User: cloudlod
    - Database: wavelog

    Use the password you choose when running the docker.

4. Hit install.

    - If Wavelog installs into a blank screen, open the base URL

## Default cronjobs

The following cronjobs are set by default through the Dockerfile They don't need to be manually enabled. They can be updated, but this is a hassle. I use these settings because they made the most sense to me. The spacing is done to ensure the scripts don't run concurrently and cause time-outs.

The set cronjobs and runtimes are:

### ClubLog upload

Every day at 00:00 and 12:00

### QRZ upload

Every day at 00:10 and 12:10

### QRZ download

Every day at 00:20 and 12:20

### eQSL sync

Every day at 00:30 and 12:30

### HRDLog upload

Every day at 00:40 and 12:40

### LotW upload

Every day at 01:00

### LotW user database update

Every day at 01:10

### ClubLog Super Check Partial

Every Monday at 01:20

### Summits on the Air (SOTA) database update

On the 1st of every month at 02:00

### World Wide Flora & Fauna (WWFF) databse update

On the 1st of every month at 02:10

### Parks on the Air (POTA) database update

On the 1st of every month at 02:20

### DOK database update

On the 1st of every month at 03:00

## Backup

While the ADIF file can be useful, it's crucial to backup the MariaDB database in regular intervals as well. This is even more important when you use more than just one station location and/or users.

You can find a good shell script for backing up MariaDB databases running on Docker here. [Link (In German)](https://www.laub-home.de/wiki/Docker_MySQL_and_MariaDB_Backup_Script)

This script is especially helpful when you run more than one MariaDB container.

Alternatively, you can use the following command to trigger a database dump manually:

```bash
docker exec wavelog-mariadb /bin/bash -c 'mariadb-dump --user wavelog --password=YOUR_PASSWORD wavelog' > /your/path/to/wavelog.sql
```

You can easily turn this command into a cronjob. If you have crontab installed, simply use this command to run a cronjob daily at 06:00:

```bash
echo "0 6 * * * docker exec wavelog-mariadb /bin/sh -c 'mariadb-dump --user wavelog --password=YOUR_PASSWORD wavelog' > /your/path/to/wavelog.sql" >> /etc/crontab
```

With that set, keep the 3-2-1 backup rule (3 copies, 2 different media, 1 copy off-site) in mind. Any backup should also at the very least keep the wavelog-config backup in mind. If you use functionalities such as displaying your QSL cards, also include wavelog-images.

## Support

Please note, this is primarily for my own setup. Feel free to use it (it should work fine). If you find issues, report them on my [Github](https://github.com/jk13xyz/wavelog_docker/issues). However, **I don't guarantee any support.**

## Source

- This Docker:

    [jk13xyz/wavelog_-_docker](https://github.com/jk13xyz/wavelog_docker)

- Wavelog:

    [wavelog/wavelog](https://github.com/wavelog/wavelog)

- wavelog_docker

    [int2001/wavelog_docker](https://github.com/int2001/wavelog_docker)
