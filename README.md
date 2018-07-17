# `production-openclinica-docker`

## Introduction

The goal of this project is to create a production-grade deployment of the Community Edition of OpenClinica [https://www.openclinica.com/community-edition-open-source-edc/](https://www.openclinica.com/community-edition-open-source-edc/) using Docker for deployment. Consequently, this will include OpenClinica webservices and a `nginx` reverse proxy with `https://` support. The `https://` certificates will be provided by Let's Encrypt.

However, be warned, I **really** don't know what I am doing *yet*. This project is for learning and to get experience. Comments and criticisms welcome. Also this is my first GitHub project of my own ...

The project is based on the work of others. I want to give especial credit to:

- Jens Piegsa. See: [https://hub.docker.com/r/piegsaj/openclinica/](https://hub.docker.com/r/piegsaj/openclinica/)

- The BRC Clinical Informatics Team. (Work not publicly available.)

However, any misuse and stupidity is likely to be mine and not theirs ...

Otherwise, credit/help is acknowledged in the various sections.

## **WARNING** -- THIS PROJECT WORKS *JUST*

- PRE-REQUISITES:
  - Docker installed as per documentation. See: [https://docs.docker.com/install/](https://docs.docker.com/install/).
  - `docker-compose` installed. See: [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/).
- USAGE:
  - Clone repository.
  - In project root execute: `docker-compose up` *if* `docker-compose` was installed so that the user could execute. See instructions for this.

- It is NOT yet production ready yet -- but getting there -- *slowly*.
- Present limitations:
  - There is no `nginx` proxy working
  - There is no Let's Encrypt support
  - Passwords etc. are public and hard-coded. Definitely don't use as is. *Eventually* there will be support for an environment variable file and directions on how to use.
  - Automatic restarting of containers on failure or re-boot of server is not implemented
  - Multiple independent instances of `OpenClinica` and underlying software is not developed. The intention is to do so.

- Suffice to say, this is a work in progress ...

## `OpenClinica`

- The OpenClinica Docker image comes from Jens Piegsa, as noted above.
- See: [https://hub.docker.com/r/piegsaj/openclinica/](https://hub.docker.com/r/piegsaj/openclinica/)

## `Postgres`

- The use of Postgres is as specified by Jens Piegsa in the above project.

## `nginx`

- I don't yet know how to set up `nginx` as I would like. Learning.
- Useful resources:
  - [https://www.humankode.com/ssl/how-to-set-up-free-ssl-certificates-from-lets-encrypt-using-docker-and-nginx](https://www.humankode.com/ssl/how-to-set-up-free-ssl-certificates-from-lets-encrypt-using-docker-and-nginx)  -- Very useful overview of using `nginx` and Let's Encrypt (among other things).

  - [https://www.thepolyglotdeveloper.com/2017/03/nginx-reverse-proxy-containerized-docker-applications/](https://www.thepolyglotdeveloper.com/2017/03/nginx-reverse-proxy-containerized-docker-applications/)  -- Another approach to proxying. Demonstrates some useful `docker-compose` commands such as `depends_on`.

- [http://www.littlebigextra.com/install-nginx-reverse-proxy-server-docker/](http://www.littlebigextra.com/install-nginx-reverse-proxy-server-docker/)  -- Provides some further useful information.
  