# Apache Superset Docker for Railway


Modified code from here: https://medium.com/towards-data-engineering/quick-setup-configure-superset-with-docker-a5cca3992b28

## Why this?

Apache Superset has Docker images for deployment but the setting of the admin user requires executing code in the container. That is not possible to set up in a Railway template. This modification supports the creation of the admin user through setting of environment variables.
