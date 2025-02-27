# Joplin Server with Caddy and PostgreSQL

This repository provides a `docker-compose` setup to deploy Joplin Server with PostgreSQL and Caddy as a reverse proxy.

## Prerequisites

Ensure you have the following installed:

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)
- DNS Record pointing to the IP docker is running on

## Setup

### 1. Configure Environment Variables

A sample environment file (`sample.env`) is provided. Copy and rename it to `.env`:

```sh
cp sample.env .env
```

Edit `.env` to set the appropriate values:

```
APP_BASE_URL=https://sample.domain.com  # The base URL where Joplin Server will be accessible
APP_PORT=22300  # Internal port used by the Joplin server
DB_CLIENT=pg  # Database client (PostgreSQL in this case)
POSTGRES_PASSWORD=secret password  # Password for the PostgreSQL database
POSTGRES_DATABASE=joplin  # Database name
POSTGRES_USER=joplin  # PostgreSQL username
POSTGRES_PORT=5432  # PostgreSQL port
POSTGRES_HOST=localhost  # Database host
BASE_PATH=/app/joplin  # Base path for storing persistent files
```

### 2. Configure Caddy

The Caddy configuration file is located at `conf/caddyfile`. Update the domain to match your server’s DNS record:

```
sample.domain.com {
    reverse_proxy app:22300
}
```

Ensure `sample.domain.com` is replaced with the actual domain that resolves to your server. This value must match `APP_BASE_URL` in `.env`.

### 3. Deploy with Docker Compose

Run the following command to start the services:

```sh
docker-compose up -d
```

This will start:

- **Caddy** (Reverse proxy)
- **PostgreSQL** (Database)
- **Joplin Server** (Application backend)

To check running containers:

```sh
docker-compose ps
```

### 4. Accessing Joplin Server

Once the containers are running, you can access Joplin Server at:

```
https://your.domain.com
```

[default username and password](https://github.com/laurent22/joplin/blob/dev/packages/server/README.md#update-the-admin-user-credentials) are:
username: admin@localhost
password: admin

and should be changed upon first login

Ensure that the domain you set in `APP_BASE_URL` resolves correctly to your server.

### 5. Stopping and Removing Containers

To stop the containers:

```sh
docker-compose down
```

To remove all data (use with caution):

```sh
docker-compose down -v
```

## Persistent Storage

The following volumes and directories are used for persistent storage:

- `${BASE_PATH}/conf` - Caddy configuration
- `${BASE_PATH}/site` - Caddy site files
- `${BASE_PATH}/database` - PostgreSQL database data
- `${BASE_PATH}/files` - Joplin server file storage
- `caddy_data` - Caddy's internal data storage
- `caddy_config` - Caddy's configuration storage

Ensure that `${BASE_PATH}` is set correctly in the `.env` file.

## Troubleshooting

- Check logs for any issues:

```sh
docker-compose logs -f
```

- Restart a specific service:

```sh
docker-compose restart <service_name>
```

Replace `<service_name>` with `caddy`, `db`, or `app` as needed.

## Additional Information
[Joplin Server Documentation](https://github.com/laurent22/joplin/blob/dev/packages/server/README.md)

## License

This repository is provided as-is under the MIT License.
