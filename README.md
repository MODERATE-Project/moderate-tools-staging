# MODERATE Staging Environment

This repository contains configuration files and tasks for the MODERATE Staging Environment - a dedicated instance for deploying and testing MODERATE tools and services before they are stable enough for integration into the main Kubernetes cluster.

## Solar Cadastre Post-Deployment Configuration

Running the deployment task available here for the Solar Cadastre is not enough to finalize configuration. The following steps need to be performed:

1. Download the SQL dump containing the seed data for the Solar Cadastre, which is available for authenticated users only on MODERATE's GCS: https://storage.cloud.google.com/moderate-common-assets/solar-cadaster-postgis-seed.sql
2. Load the SQL seed by running the `load-cloud-sql-dump` task in [MODERATE-Project/moderate-infrastructure](https://github.com/MODERATE-Project/moderate-infrastructure)
3. Follow the [steps described in the MODERATE-Project/solar-cadastre](https://github.com/MODERATE-Project/solar-cadastre/blob/main/docs/geoserver.md) repository to configure GeoServer to publish the data from the restored database

> [!TIP]
> Please note that the URL of the Cloud SQL proxy configured in GeoServer for accessing the Solar Cadastre PostGIS database should be a private IP address, published as a service of type _Internal Load Balancer_ named `cloud-sql-internal-service`.

> [!TIP]
> The default configuration environment variables should be `SOLAR_CADASTRE_GEOSERVER_SCHEME_HOST=https://geoserver.moderate.cloud` and `SOLAR_CADASTRE_GEOSERVER_PATH=/geoserver/GeoModerate/ows`. This means that the workspace name should be `GeoModerate`.