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

To start the container run `docker-compose -f docker-compose.dev.yml up -d erddap`. Then go to http://localhost:8080. To check that the service is up run `docker-compose exec erddap bash` and check to see that there's data in
the `logs/` directory. Tomcat takes a while to start up, don't be surprised if
you don't see anything.

## Loading data into ERDDAP

> This process will guide you on how to start a local ERDDAP server and load new datasets.

Before loading data into ERDDAP it's important to get familiar with ERDDAP's data structure and data types. [ERDDAP provides extensive documentation on that topic here.](https://coastwatch.pfeg.noaa.gov/erddap/download/setupDatasetsXml.html)

ERDDAP also provides a few tools to help you create the dataset's XML file, and the data attribute structure/data descriptor structure. 

To create a XML block for a dataset, run:
```bash
#development
docker-compose -f docker-compose.dev.yml run generate_dataset_xml

#production
docker-compose run generate_dataset_xml
```

This will run the script `GenerateDatasetXml.sh` provided by ERDDAP. This is an interactive tool that will ask you to enter some information about the dataset. We place data files in `/erddapData/`, which is mounted to `isilon_erddap/erddap_data`. Make sure you have downloaded the `isilon_erddap` folder from Google Drive. If you don't have that, ask an administrator for the link. 

The logs will be saved at `isilon_erddap/erddap_data/GenerateDatasetsXml.log`. You can check the logs to help you debugging the dataset. If the XML block is generated successfully, it will be printed in that log file.

After that's done, copy the XML dataset block and paste it into `erddap-dev/datasets.xml` within the `<erddapDatasets></erddapDatasets>` tag.

The XML block will have the dataset ID. This ID is used to generate the dataset attribute structure (DAS) and the dataset descriptor structure (DDS). To generate the DAS/DDS, run:

```bash
#development
docker-compose -f docker-compose.dev.yml run generate_data_structure 

#production
docker-compose run generate_data_structure 
```

This will run ERDDAP's `DasDds.sh` script. It will ask you the enter the ID of your dataset, which was generated in the previous step. The logs for this script will be at `isilon_erddap/erddap_data/DasDds.log`. If that is completed successfully, you can start your local ERDDAP server:

```bash
#development
docker-compose -f docker-compose.dev.yml up -d erddap

#production
docker-compose up -d erddap
```

You can then visit `localhost:8080/erddap/index.html`. The server might take a few minutes to start and load all datasets.

If you can see your dataset in your local ERDDAP server, you're ready to start a Pull Request to the production server.

To do that: 
- paste the dataset's XML block in the production `erddap/dataset.xml` file in this repo.
- add a link to the raw data files in your PR's description.

For admins:
- After merging the PR, follow the [Loading data into ERDDAP](#loading-data-into-erddap) process again, but with the production server.


## Costumize ERDDAP text

To costumize ERDDAP's appearance and text, edit the `content/erddap/setup.xml` or `content/erddap-dev/setup.xml` file. Check the `messages.xml` file for costumizable blocks and add those at the end of `setup.xml`. There are currently two examples in `setup.xml`:

- `<startBodyHtml5><startBodyHtml5>`: Is responsible to changes made to the banner.
- `<theShortDescriptionHtml></theShortDescriptionHtml>`: Contains the text on the left column of the front page. To change this, you can remove `[standardShortDescriptionHtml]` and add content in `html` format.

It's recommended to use the `erddap-dev/setup.xml` and check if your changes are working correctly. (See development workflow above). When everything looks correct, add those changes to `erddap/setup.xml`

[1]: https://hub.docker.com/r/axiom/docker-erddap
