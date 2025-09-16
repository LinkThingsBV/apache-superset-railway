# Apache Superset Docker for Railway

This repository provides a Docker image that can be deployed directly to [Railway](https://railway.com/) to run [Apache Superset](https://superset.apache.org/).  It extends the official `apache/superset` image with:

- automatic creation of the Superset admin user through environment variables (so no manual container exec is required);
- database client libraries for PostgreSQL, MySQL/MariaDB and Microsoft SQL Server/Azure SQL;
- a lightweight Superset configuration file that reads all secrets from the Railway environment.

> ℹ️  The Docker image now bundles Microsoft's ODBC Driver 18 together with `pyodbc`, which allows Superset to use **Azure SQL Database** as its metadata store or as a SQL Server datasource inside Superset.

## Deploying on Railway

1. Create a new Railway project and select “Deploy from GitHub repository”.
2. Point Railway to this repository and trigger a deploy.
3. Configure the following environment variables inside Railway:

   | Variable | Purpose |
   | --- | --- |
   | `ADMIN_USERNAME` | Login for the Superset admin user. |
   | `ADMIN_EMAIL` | Email address for the admin account. |
   | `ADMIN_PASSWORD` | Password for the admin user. |
   | `SECRET_KEY` | Flask secret key used by Superset. Generate a long random string. |
   | `DATABASE` | SQLAlchemy connection string for Superset's metadata database. |

   The entrypoint script fails fast when these variables are missing so you immediately know if something is misconfigured.

4. Expose port `8088` (the default Superset web server port) in your Railway service settings.

Once the deploy finishes Superset will be reachable using the Railway-generated URL. The admin user defined through the variables above will already be created.

## Using Azure SQL as the metadata database

Set the `DATABASE` environment variable to an Azure SQL SQLAlchemy URI that uses the bundled ODBC Driver 18.  A typical connection string looks like this:

```
mssql+pyodbc://<username>:<password>@<server>.database.windows.net:1433/<database>?driver=ODBC+Driver+18+for+SQL+Server&Encrypt=yes&TrustServerCertificate=no
```

- Replace `<username>`, `<password>`, `<server>` and `<database>` with your Azure SQL values.
- URL-encode special characters in the password (for example `@` becomes `%40`).
- Keep `Encrypt=yes` so the connection complies with Azure's security requirements.  Set `TrustServerCertificate=no` unless you have a specific reason to disable certificate validation.

You can use the same URI format when defining Azure SQL datasets inside Superset.

## Customising the image

- Additional Python dependencies can be added to `requirements.txt`.
- Further Superset configuration can be appended to `config/superset_config.py`.  The file already exposes the `DATABASE` and `SECRET_KEY` environment variables.
- The startup sequence lives in `config/superset_init.sh`.  The script exits on errors to make debugging deployment issues easier.

## Credits

The base idea for automating the admin user creation is inspired by the article [Quick setup: Configure Superset with Docker](https://medium.com/towards-data-engineering/quick-setup-configure-superset-with-docker-a5cca3992b28).
