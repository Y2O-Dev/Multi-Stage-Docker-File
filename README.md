# Multi-Stage-Docker-File
This Dockerfile is used to build a Docker image in two stages, each serving a specific purpose. The resulting image is intended to be lightweight and efficient. Below is a brief explanation of each stage and its purpose

## Stage 1: Build Stage
In this stage, the Docker image starts with the base image of Ubuntu 20.04. This stage aims to set up a clean environment for building the desired software.

. ` apt-get update -y `: Updates the package manager repository with the latest information.

. ` apt-get install -y --no-install-recommends `: Installs the specified packages (curl and osmium-tool) without installing any recommended additional packages.

. ` && rm -rf /var/lib/apt/lists/* `: Clean up the package manager's cache to reduce the image size.

. ` mkdir /osmfiles /merged `: Creates two directories, /osmfiles and /merged, to store OpenStreetMap (OSM) data files.

. ` && curl -o /osmfiles/monaco.osm.pbf http://download.geofabrik.de/europe/monaco-latest.osm.pbf`: Downloads an OSM data file for Monaco and places it in the /osmfiles directory.

. `&& curl -o /osmfiles/andorra.osm.pbf http://download.geofabrik.de/europe/andorra-latest.osm.pbf`: Downloads an OSM data file for Andorra and places it in the /osmfiles directory.

. `&& osmium merge /osmfiles/monaco.osm.pbf /osmfiles/andorra.osm.pbf -o /merged/merged.osm.pbf`: Merges the OSM data files for Monaco and Andorra into a single file named merged.osm.pbf in the /merged directory.

## Stage 2: Final Stage
In this final stage, the Docker image is further optimized for production use:

.  FROM alpine AS final switches to a minimal Alpine Linux base image to create a lightweight final image.

. ` mkdir /merged `: Creates a directory named /merged in the final image.

. `COPY --from=build /merged /merged `: Copies the merged OSM data files from the build stage into the /merged directory of the final image.

The resulting Docker image contains the merged OSM data files and is ready for use in a production environment. This two-stage build process helps keep the final image small and efficient by eliminating unnecessary build artifacts and dependencies.
