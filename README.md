# `production-openclinica-docker`

## Introduction

The goal of this project is to create a production-grade deployment of the Community Edition of OpenClinica [https://www.openclinica.com/community-edition-open-source-edc/](https://www.openclinica.com/community-edition-open-source-edc/) using Docker for deployment. Consequently, this will include OpenClinica webservices and a `nginx` reverse proxy with `https://` support. The `https://` certificates will be provided by Let's Encrypt.

However, be warned, I **really** don't know what I am doing *yet*. This project is for learning and to get experience. Comments and criticisms welcome. Also this is my first GitHub project of my own ...

The project is based on the work of others. Specifically:

- Jens Piegsa. See: [https://hub.docker.com/r/piegsaj/openclinica/](https://hub.docker.com/r/piegsaj/openclinica/)

- The BRC Clinical Informatics Team. (Work not publicly available.)