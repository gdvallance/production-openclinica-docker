# `production-openclinica-docker`

## Introduction

The primary goal of this project is to create a production-grade deployment of the latest version of the  [Community Edition of OpenClinica](https://www.openclinica.com/community-edition-open-source-edc/) (v3.14) using [Docker](https://www.docker.com/) and `docker-compose` for deployment.

The secondary goal is to set up a system that supports multiple independent instances of OpenClinica databases behind a reverse proxy (`nginx`) with `https://` termination, via [Let's Encrypt](https://letsencrypt.org/) SSL certificates.

As of writing (25th July, 2018), both goals have been achieved (more or less). It is certainly not perfect, and rough around the edges. However, it *is* a plausible v1.0.0 ... While there is not a formal road-map yet, there is one in my head, so improvements are coming.

The pattern to support multiple instances of OpenClinica is to have a root URL with databases as branches, <ROOT_URL>/<OPEN_CLINICA_DATABASE>. E.g.

- Landing Page: `databases.oxford-myeloma.org.uk`;
- Database #1: `databases.oxford-myeloma.org.uk/XXXX` and
- Database #2: `databases.oxford-myeloma.org.uk/YYYY`.

However, be warned, I **really** don't know what I am doing *yet*. This project is for learning and to get experience. Comments and criticisms welcome. Also this is my first GitHub project of my own ...

The project is based on the work of others. I want to give especial credit to:

- Jens Piegsa. See: [https://hub.docker.com/r/piegsaj/openclinica/](https://hub.docker.com/r/piegsaj/openclinica/)

- The BRC Clinical Informatics Team, particularly Ollie Freeman, whose assistance and expertise was essential to get this working. I am extremely grateful for his help and training. I learnt a lot ...

Any errors etc., are likely to be mine.

## Pre-Requisites

- Linux Server (bare-metal or VM). Project developed on `Ubuntu 16.04` locally on various machines and implemented on an `Ubuntu 18.04` VM on [Digital Ocean](https://www.digitalocean.com) (4 GB Memory / 80 GB Disk).
- `git` installed as per documentation for your development and production machines. See: [https://git-scm.com/](https://git-scm.com/).
- Docker installed as per documentation. See: [https://docs.docker.com/install/](https://docs.docker.com/install/).
- `docker-compose` installed as per documentation. See: [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/).

### Optional

- An editor or IDE. I used [Visual Studio Code](https://code.visualstudio.com/), with the following extensions:
  - Docker: `peterjausovec.vscode-docker`
  - nginx.conf: `shanoor.vscode-nginx`
  - YAML Support by Red Hat`redhat.vscode-yaml`
  - GitLens: `eamodio.gitlens`  

## Usage

### Clone repository

- In project root:

  ```bash

    git clone https://github.com/gdvallance/production-openclinica-docker.git

  ```

### Adapt code-base

#### General Considerations

- This code-base has three branches: (1) `master`; (2) `develop`; and (3) `production`. **These are NOT used conventionally**.
  - `master`: a non-functional repository with templates for DEVELOPMENT & PRODUCTION environment and their deployment.
  - `develop`: a working development environment, suitable for local development on any machine.
  - `production`: a working production environment, suitable for deployment on a production server.

#### `html` folder

- This folder holds the `html` for the landing page for the installation. I use it as a jumping off point for the various databases of interest.
- I just have an `index.html` page with styling provided by the ultra simple `getskeleton` css framework. See: [http://getskeleton.com/](http://getskeleton.com/).
- Adapt to your needs ...
- **NB** more complexity will require changing (at the very least) the `nginx.conf` file.

#### `letsencrypt` folder

- Delete the `create_your_own_pem_file_and_remove_this.txt` file.
- Create a suitable  `.pem` file.
- In project root execute:

  ```bash
  cd letsencrypt/dh-param
  rm create_your_own_pem_file_and_remove_this.txt
  sudo openssl dhparam -out dhparam-2048.pem 2048
  ```

#### `models` folder

- Nothing to do. Just a diagram of what I am attempting to do.

#### `openclinica` folder

- Nothing to do.
- This folder provides a *de-novo* build of OpenClinica v3.14 based on the downloads from the OpenClinica Website. The `Dockerfile` and `run.sh` are adapted from Jen Piega's work.

#### `docker-compose.yml` file

- The file works in *both* DEVELOPMENT, and PRODUCTION environments.
- This infrastructure (at this stage) requires 1 nginx proxy `nginx-proxy`; 1 or more OpenClinica (`tomcat`) instances; and 1 or more associated `postgres` instances.
- A complete OpenClinica database requires and OpenClinica (`tomcat`) and `postgres` container.
- In the file below:
  - If you are NOT using `https://` then delete the following lines:
    - /docker-volumes/etc/letsencrypt/live/ZZZZ/fullchain.pem:/etc/letsencrypt/live/ZZZZ/fullchain.pem
    - /docker-volumes/etc/letsencrypt/live/ZZZZ/privkey.pem:/etc/letsencrypt/live/ZZZZ/privkey.pem
  - If you ARE using `https://` then replace ZZZZ with the domain name for which you have obtained Let's Encrypt SSL certificates as *per* the instructions in the `nginx_letsencrypt.conf` notes below.
  - To create and deploy TWO databases, replace XXXX & YYYY with the names of the databases you wish.
  - To add another OpenClinica database copy and paste the `XXXX-oc` & `XXXX-pg` services and then rename the `XXXX` accordingly. Then add the pertinent entries in the volumes section.

```yml
version: '3.6'

services:

  nginx-proxy:
    image: nginx:latest
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
      - ./html:/usr/share/nginx/html
      - ./letsencrypt/dh-param/dhparam-2048.pem:/etc/ssl/certs/dhparam-2048.pem
      - /docker-volumes/etc/letsencrypt/live/ZZZZ/fullchain.pem:/etc/letsencrypt/live/ZZZZ/fullchain.pem
      - /docker-volumes/etc/letsencrypt/live/ZZZZ/privkey.pem:/etc/letsencrypt/live/ZZZZ/privkey.pem
    restart: unless-stopped

  XXXX-oc:
    build: ./openclinica
    environment:
      - DATABASE_NAME=XXXX
      - DB_HOST=XXXX-pg  
    volumes:
      - XXXX_oc-data:/usr/local/tomcat/openclinica.data
    restart: unless-stopped

  XXXX-pg:
    image: postgres:9.5
    environment:
      - POSTGRES_INITDB_ARGS="-E 'UTF-8'"
      - POSTGRES_PASSWORD=postgres123
    volumes:
      - XXXX_ocdb-data:/var/lib/postgresql/data
      - $PWD/init-db.sh:/docker-entrypoint-initdb.d/init-db.sh
    restart: unless-stopped

  YYYY-oc:
    build: ./openclinica
    environment:
      - DATABASE_NAME=YYYY
      - DB_HOST=YYYY-pg
    volumes:
      - YYYY_oc-data:/usr/local/tomcat/openclinica.data
    restart: unless-stopped

  YYYY-pg:
    image: postgres:9.5
    environment:
      - POSTGRES_INITDB_ARGS="-E 'UTF-8'"
      - POSTGRES_PASSWORD=postgres123
    volumes:
      - YYYY_ocdb-data:/var/lib/postgresql/data
      - $PWD/init-db.sh:/docker-entrypoint-initdb.d/init-db.sh
    restart: unless-stopped

volumes:
  XXXX_oc-data:
  XXXX_ocdb-data:
  YYYY_oc-data:
  YYYY_ocdb-data:
```

- You may wish to change the `POSTGRES_PASSWORD` environment variable to something different. However, bear in mind the `postgres` instances are NOT exposed to the host.

#### `init-db.sh`

- This need not be changed unless you are concerned with using the standard default passwords for the `postgres` database OpenClinica uses.
- However, if you do change it you will also need to change the environment variable `DB_PASS` in the `Dockerfile` in the `openclinica` folder.
- One of my TODOs is to implement the `DB_PASS` environment variable into this file so that it can be properly overridden in the `docker-compose.yml` file.

#### `nginx_development.conf` file

- The `nginx_development.conf` file is a template for a local development environment based on `localhost`. It does not support `https://`
- To implement, rename to `nginx.conf`

### `nginx_letsencrypt.conf` file

- The `nginx_letsencrypt.conf` file is a template that is needed to obtain Let's Encrypt SSL files.
- To implement, rename to `nginx.conf` and then customise according to the instructions.
- Spin up the infrastructure and once running execute in project root after replacing YOUR_EMAIL_ADDRESS and YOUR_EMAIL with your values:

  ```docker
  sudo docker run -it --rm \
  -v /docker-volumes/etc/letsencrypt:/etc/letsencrypt \
  -v /docker-volumes/var/lib/letsencrypt:/var/lib/letsencrypt \
  -v /home/gdvallance/production-openclinica-docker/html:/data/letsencrypt \
  -v "/docker-volumes/var/log/letsencrypt:/var/log/letsencrypt" \
  certbot/certbot \
  certonly --webroot \
  --email YOUR_EMAIL_ADDRESS --agree-tos --no-eff-email \
  --webroot-path=/data/letsencrypt \
  -d YOUR_DOMAIN
  ```

#### `nginx_production.conf` file

- The `nginx_production.conf` file is a template for a production environment based on a deployment on a server that already has DNS for the domain name sorted.
- **IT DEPENDS ON** `nginx_letsencrypt.conf` being used and the Let's Encrypt SSL certificates being obtained. In other words, you will need to spin up the containers, obtain the certificates, and spin them down before customizing.
- To implement, following the instructions in the comments and rename to `nginx.conf`.

### Build and pull down images and spin up containers

- Once the appropriate customization's as *per* instructions are done then.
- In project root:

  ```bash
  # If you want to see log files
  docker-compose up

  # Or
  docker-compose -d up
  ```

  **NB**: The images (even when present) and the containers can take a long time to spin up -- 2-5 min. *May* be shorter, but don't count on it. Using `docker-compose up` is useful to see when things complete.

### WARNINGS

- This is still a work in progress.

- Sensitive configurations and passwords are visible in this code-base. Ultimately, these will be abstracted away and put into uncommitted config files and/or Docker `secrets`. But ...
  
  **NB** In the mean-time, having these present are (arguably) not an issue because they require access to the machines(s) in question. Such access is secured. My development machines are separate VMs LUKs encrypted and spun down when not in use and my production machine only accepts non-root SSH login via public/private key.

- OpenClinica is sensitive to whether `postgres` is ready when it deploys. No mechanism is presently in place to ensure the proper sequence. To date, I have not experienced any problems.

- If the host machine is re-booted or shutdown without the containers being shutdown first, then you can get a 'Waiting for changelog lock' error. This is caused by a corrupted table on `postgres` giving `liquibase` a hissy-fit. See: [https://stackoverflow.com/questions/15528795/liquibase-lock-reasons](https://stackoverflow.com/questions/15528795/liquibase-lock-reasons). Have not tested an elegant fix. You can resolve the error by bring down the containers and deleting the `postgres` persistance volume. E.g. `panbordex_ocdb-data` and restoring from backups if you have them.

- Webservices are installed in theory but have not been tested if they work. Unlikely that they do. Getting these operational is on the TODO list.

## References

- [https://github.com/JensPiegsa/OpenClinica](https://github.com/JensPiegsa/OpenClinica) -- This was the foundation of the project. I initially used the image from the Docker Repository (see below) however, for various reasons I had to re-develop and build a later version, but it was through adapting Jens' code, especially his `Dockerfile` and `run.sh`.

- [https://hub.docker.com/r/piegsaj/openclinica/](https://hub.docker.com/r/piegsaj/openclinica/) -- Jen Piegsa's Docker Hub image.

- [https://www.humankode.com/ssl/how-to-set-up-free-ssl-certificates-from-lets-encrypt-using-docker-and-nginx](https://www.humankode.com/ssl/how-to-set-up-free-ssl-certificates-from-lets-encrypt-using-docker-and-nginx) -- Very useful overview of using `nginx` and Let's Encrypt (among other things). I adapted the code to get `https://` working.
  