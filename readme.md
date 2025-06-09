# Basic Docker Environment

![GitHub Repo stars](https://img.shields.io/github/stars/yourusername/basic_docker?style=social)
![GitHub license](https://img.shields.io/github/license/yourusername/basic_docker)
![GitHub last commit](https://img.shields.io/github/last-commit/yourusername/basic_docker)
![GitHub issues](https://img.shields.io/github/issues/yourusername/basic_docker)

A simple and configurable Docker environment for PHP development. This repository provides a basic configuration that can be easily extended to support different development projects.

## Features

- **PHP 8.1.13** with FPM on Alpine Linux
- **Nginx** as web server
- **Traefik** as reverse proxy with SSL support
- **Xdebug 3.2.0** configured for debugging
- Support for common PHP extensions:
  - bcmath
  - pcntl
  - pdo_mysql
  - mysqli
  - imagick

## Requirements

- Docker
- Docker Compose

## Project Structure

```
.
├── docker/
│   ├── nginx/
│   │   └── vhost/
│   │       └── site.conf
│   └── php/
│       ├── conf.d/
│       │   ├── error_reporting.ini
│       │   └── xdebug.ini
│       ├── Dockerfile
│       ├── php-fpm.conf
│       └── php.ini
├── www/
│   └── index.php
├── docker-compose.yml
├── traefik.yml
└── .env
```

## Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/basic_docker.git
   cd basic_docker
   ```

2. Copy the `.env.dist` file to `.env` and customize the environment variables:
   ```bash
   cp .env.dist .env
   ```

3. Start the containers:
   ```bash
   docker-compose up -d
   ```

4. Access the application via browser:
   ```
   https://site.test
   ```
   Note: Make sure to add `site.test` to your local hosts file.

## Configuration

### Environment Variables

The following variables can be configured in the `.env` file:

- `DOMAIN`: The main domain of the application (default: site.test)
- `PHP_FPM_HOST_PORT`: The host port for PHP-FPM (default: 9000)
- `NGINX_HOST_PORT`: The host port for Nginx (default: 80)
- `TRAEFIK_HOST_PORT`: The host port for the Traefik dashboard (default: 8080)

### Xdebug

#### Configuration for Mac/Windows

The default Xdebug configuration is set to work with Mac and Windows:

```ini
zend_extension=xdebug

[xdebug]
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=host.docker.internal
xdebug.client_port=9003
xdebug.idekey=PHPSTORM
```

#### Configuration for Linux

For Linux, you need to modify the Xdebug configuration since `host.docker.internal` is not available by default. Here's how to configure Xdebug for Linux:

1. Modify the `docker/php/conf.d/xdebug.ini` file:

```ini
zend_extension=xdebug

[xdebug]
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=172.17.0.1
xdebug.client_port=9003
xdebug.idekey=PHPSTORM
```

2. Alternatively, you can use the IP address of your network interface:

```ini
xdebug.client_host=192.168.x.x  # Replace with your local IP address
```

3. Another option is to use the host name:

```bash
# Add this line to your docker-compose.yml in the PHP service
extra_hosts:
  - "host.docker.internal:host-gateway"
```

Note: The current configuration already includes `host.docker.internal:host-gateway` which should work on Linux with recent Docker Compose (v2.x+).

## Customization

### Adding New Services

To add new services, modify the `docker-compose.yml` file. For example, to add a MySQL database:

```yaml
services:
  # ... existing services ...
  
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: mydb
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - ./docker/mysql/data:/var/lib/mysql
    ports:
      - "3306:3306"
```

### Extending the PHP Dockerfile

To add new PHP extensions or packages, modify the `docker/php/Dockerfile` file.

## Usage

### Accessing Containers

```bash
# Access the PHP container
docker-compose exec php sh

# Access the Nginx container
docker-compose exec nginx sh
```

### Viewing Logs

```bash
# View logs for all containers
docker-compose logs

# View logs for a specific container
docker-compose logs php
```

## Troubleshooting

### Xdebug Not Connecting

1. Verify that the IP address configured in `xdebug.client_host` is correct
2. Make sure the firewall is not blocking the connection on port 9003
3. Check that the IDE is listening for debug connections

### Permission Issues

If you encounter permission issues with files, you can change the file owner in the container:

```bash
docker-compose exec php chown -R www-data:www-data /var/site/www
```

## Contributing

Contributions are welcome! Feel free to open an issue or submit a pull request.

## License

This project is released under the MIT license.

