# MODERATE Staging Environment

This repository contains configuration files and tasks for the MODERATE Staging Environment - a dedicated instance for deploying and testing MODERATE tools and services before they are stable enough for integration into the main Kubernetes cluster.

## Solar Cadastre Post-Deployment Configuration

Running the deployment task available here for the Solar Cadastre is not enough to finalize configuration. The following steps need to be performed:

1. Download the v3 database archive and GeoServer raster from the public [Solar Cadastre dataset release](https://github.com/MODERATE-Project/moderate-tools-staging/releases/tag/solar-data-2026-10-06). V2 is available for older deployments.

   | Dataset | Download |
   | --- | --- |
   | PostGIS seed v3 (default, exported 2026-04-09) | [solar-cadaster-postgis-seed-v3.dump](https://github.com/MODERATE-Project/moderate-tools-staging/releases/download/solar-data-2026-10-06/solar-cadaster-postgis-seed-v3.dump) |
   | PostGIS seed v2 (older version, exported 2025-05-08) | [solar-cadaster-postgis-seed-v2.dump](https://github.com/MODERATE-Project/moderate-tools-staging/releases/download/solar-data-2026-10-06/solar-cadaster-postgis-seed-v2.dump) |
   | GeoServer PV generation raster | [solar-cadaster-pv-generation-cells.tif](https://github.com/MODERATE-Project/moderate-tools-staging/releases/download/solar-data-2026-10-06/solar-cadaster-pv-generation-cells.tif) |

   Check the downloads against the SHA-256 checksums in the release notes.

2. Restore the archive into an empty PostgreSQL database with PostGIS and PostGIS Raster available. Both archives use PostgreSQL's custom format and were exported with PostgreSQL 14.15. Restore v3 with:

   ```bash
   pg_restore --no-owner --no-acl --exit-on-error \
     --dbname=YOUR_DATABASE solar-cadaster-postgis-seed-v3.dump
   ```

   Replace `YOUR_DATABASE` with your database name. Set the host, port, user, and authentication through PostgreSQL connection options or environment variables.

3. Follow the [Solar Cadastre GeoServer guide](https://github.com/MODERATE-Project/solar-cadastre/blob/main/docs/geoserver.md) to publish the restored database and raster layers. Use the `.tif` downloaded above when the guide asks for the raster file.

> [!TIP]
> Please note that in the case of the default configuration of the MODERATE platform:
> * The URL of the Cloud SQL proxy configured in GeoServer for accessing the Solar Cadastre PostGIS database should be a private IP address, published as a service of type _Internal Load Balancer_ named `cloud-sql-internal-service`
> * The default value of `SOLAR_CADASTRE_GEOSERVER_SCHEME_HOST` should be `https://geoserver.moderate.cloud`
> * The default value of `SOLAR_CADASTRE_GEOSERVER_PATH` should be `/geoserver/GeoModerate/ows`. This means that the workspace name created for the Solar Cadastre during the configuration phase should be `GeoModerate`
