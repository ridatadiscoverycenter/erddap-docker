# ERDDAP Docker

This repository contains the common setup files for the
[axiom/docker-erddap][1] image. This image starts up the ERDDAP service and
serves it using Apache Tomcat. ERRDAP is a Java application is a data server
that provides a simple interface for downloading datasets.

## Development

The configuration in this repository is designed for deployments. If you'd like
to test this image locally you'll need to edit the `docker-compose.yml` and 
`content/erddap/setup.xml`.

In `docker-compose.yml` change the port from `127.0.0.1:8080:8080` to `8080`.

In `content/erddap/setup.xml` change the `baseUrl` field from
`https://pricaimcit.services.brown.edu` to `http://localhost:8080`.

## Running

To start the container run `docker-compose up -d`. To check that the service is
up run `docker-compose exec erddap bash` and check to see that there's data in
the `logs/` directory. Tomcat takes a while to start up, don't be surprised if
you don't see anything.

[1]: https://hub.docker.com/r/axiom/docker-erddap
