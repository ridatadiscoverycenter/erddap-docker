# ERDDAP Docker

This repository contains the common setup files for the
[axiom/docker-erddap][1] image. This image starts up the ERDDAP service and
serves it using Apache Tomcat. ERRDAP is a Java application is a data server
that provides a simple interface for downloading datasets.

## Development

The configuration in this repository is designed for deployments. If you'd like
to test this image locally you can use the `docker-compose.dev.yml`. 
This file differs from the production compose in two aspects:

1. The port mapping is changed from `127.0.0.1:8080:8080` to `8080`.

2. In `content/erddap/setup.xml` the `baseUrl` field will point to
to `http://localhost:8080` instead of `https://pricaimcit.services.brown.edu`.

To run ERDDAP with real data locally, you'll need to grab the data from
`pricaimcit.services.brown.edu:/isilon_erddap/buoy_data` and put it 
in a folder called `buoy_data` in the root of this project.

## Running Locally

To start the container run `docker-compose -f docker-compose.dev.yml up -d`. Then go to http://localhost:8080. To check that the service is up run `docker-compose exec erddap bash` and check to see that there's data in
the `logs/` directory. Tomcat takes a while to start up, don't be surprised if
you don't see anything.

## Costumize ERDDAP text

To costumize ERDDAP's appearance and text, edit the `content/erddap/setup.xml` or `content/erddap-dev/setup.xml` file. Check the `messages.xml` file for costumizable blocks and add those at the end of `setup.xml`. There are currently two examples in `setup.xml`:

- `<startBodyHtml5><startBodyHtml5>`: Is responsible to changes made to the banner.
- `<theShortDescriptionHtml></theShortDescriptionHtml>`: Contains the text on the left column of the front page. To change this, you can remove `[standardShortDescriptionHtml]` and add content in `html` format.

It's recommended to use the `erddap-dev/setup.xml` and check if your changes are working correctly. (See development workflow above). When everything looks correct, add those changes to `erddap/setup.xml`

[1]: https://hub.docker.com/r/axiom/docker-erddap
