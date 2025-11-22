# YOURLS Docker Japanese Edition

A Docker Compose environment for easy setup of YOURLS with MySQL 8.0, including Japanese localization files (YOURLS-ja_JP).

## Overview

This project provides an easy way to run [YOURLS](https://yourls.org/) (Your Own URL Shortener) in Docker containers. Features include:

- **Docker Compose**: Manage YOURLS and MySQL 8.0 with a single configuration file
- **Japanese Support**: Full Japanese localization via YOURLS-ja_JP
- **Easy Setup**: Flexible configuration through environment variables
- **Data Persistence**: Data protection through volume mounting

## License

This project is released under the MIT License.

- **YOURLS**: MIT License
- **YOURLS-ja_JP**: MIT License
- **This Project**: MIT License (see [LICENSE.txt](LICENSE.txt) for details)

## Requirements

- Docker
- Docker Compose
- Git

## Installation

### 1. Clone the Repository

```bash
git clone <repository-url>
cd yourls
```

### 2. Configure Environment Variables

Copy `.env.example` to create a `.env` file and modify settings as needed.

```bash
cp .env.example .env
```

Key settings in the `.env` file:

```bash
# YOURLS site URL (for external access)
YOURLS_SITE=http://localhost

# YOURLS admin username and password
YOURLS_USER=admin
YOURLS_PASS=admin

# Language setting (ja_JP: Japanese)
YOURLS_LANG=ja_JP

# Timezone offset (9 for Japan Standard Time)
YOURLS_HOURS_OFFSET=9

# External access port (default: 80)
YOURLS_EXTERNAL_PORT=80

# MySQL settings
MYSQL_ROOT_PASSWORD=example
MYSQL_DATABASE=yourls
MYSQL_USER=yourls
MYSQL_PASSWORD=yourls
```

**For security, always change passwords in production environments.**

### 3. Start Docker Containers

```bash
docker compose up -d
```

Initial startup may take time for image downloads and database initialization.

### 4. YOURLS Setup

Access `http://localhost/admin/` in your browser to complete the initial setup.

1. Click the "Install YOURLS" button
2. Database tables will be created automatically
3. Log in with the username and password configured in `.env`

## Usage

### Start Containers

```bash
docker compose up -d
```

### Stop Containers

```bash
docker compose down
```

### View Container Logs

```bash
# All containers
docker compose logs -f

# YOURLS container only
docker compose logs -f yourls

# MySQL container only
docker compose logs -f db
```

### Restart Containers

```bash
docker compose restart
```

## Database Initialization

To completely delete the database and return to initial state, follow these steps.

### ⚠️ Warning

This operation will **permanently delete all shortened URLs and data**. Always create a backup before proceeding.

### Initialization Steps

1. **Stop containers and remove volumes**

```bash
docker compose down --volumes
```

2. **Delete MySQL data directory**

```bash
# Windows (PowerShell)
Remove-Item -Recurse -Force .\volumes\mysql

# macOS/Linux
rm -rf ./volumes/mysql
```

3. **Restart containers**

```bash
docker compose up -d
```

4. **Re-setup YOURLS**

Access `http://localhost/admin/` in your browser and perform the initial setup again.

## Project Structure

```
.
├── config/
│   ├── config.php              # YOURLS configuration file
│   ├── config-sample.php       # Sample configuration file
│   ├── languages/
│   │   └── ja_JP.mo           # Japanese translation file
│   ├── plugins/               # Plugin directory
│   └── pages/                 # Custom pages
├── volumes/
│   └── mysql/                 # MySQL data (auto-generated)
├── .env                       # Environment variables (not in git)
├── .env.example              # Environment variables sample
├── docker-compose.yml        # Docker Compose configuration
├── LICENSE.txt               # License file
└── README.md                 # This file
```

## Enabling Plugins

To enable plugins, follow these steps from the YOURLS admin interface:

1. Access `http://localhost/admin/` in your browser and log in
2. Click "Manage Plugins" in the top menu
3. Click the "Activate" button for the plugin you want to enable

Available sample plugins:
- `random-shorturls` - Generate random short URLs
- `hyphens-in-urls` - Enable hyphens in URLs
- `random-bg` - Random background patterns
- `sample-toolbar` - Custom toolbar sample

## Troubleshooting

### Port 80 Already in Use

If port 80 is already in use, change `YOURLS_EXTERNAL_PORT` in the `.env` file.

```bash
YOURLS_EXTERNAL_PORT=8080
```

After changing, access via `http://localhost:8080/admin/`.

### Database Connection Error

Check if the database container is running:

```bash
docker compose ps
```

Verify that all containers are in the `Up` state.

### Japanese Not Displaying

1. Verify `YOURLS_LANG=ja_JP` is set in the `.env` file
2. Confirm `config/languages/ja_JP.mo` exists
3. Restart containers: `docker compose restart`

## Backup

### Database Backup

```bash
docker compose exec db mysqldump -u yourls -pyourls yourls > backup.sql
```

### Database Restore

```bash
docker compose exec -T db mysql -u yourls -pyourls yourls < backup.sql
```

## Reference Links

- [YOURLS Official Site](https://yourls.org/)
- [YOURLS GitHub](https://github.com/YOURLS/YOURLS)
- [YOURLS-ja_JP](https://github.com/YOURLS/YOURLS-ja-JP)
- [Docker Official Documentation](https://docs.docker.com/)

## Contribution

Please report bugs or suggest features via GitHub Issues.

## License

MIT License - See [LICENSE.txt](LICENSE.txt) for details.