# WordPress with MySQL, Nginx, and SSL

A Docker Compose project that sets up a complete WordPress environment with MySQL database, Nginx reverse proxy, and SSL encryption.

## Project Structure

```
wordpress/
├── docker-compose.yml
├── nginx/
│   ├── conf.d/
│   │   └── wordpress.conf
│   └── certs/
│       ├── cert.pem
│       └── key.pem
└── README.md
```

## Prerequisites

- Docker
- Docker Compose
- OpenSSL (for certificate generation)

## Quick Start

1. **Generate SSL Certificates**
   ```bash
   mkdir -p nginx/certs
   openssl req -x509 -newkey rsa:4096 -nodes -keyout nginx/certs/key.pem -out nginx/certs/cert.pem -days 365
   ```

2. **Start the Application**
   ```bash
   docker-compose up -d
   ```

3. **Access WordPress**
   - HTTPS: https://localhost
   - HTTP requests are automatically redirected to HTTPS

## Services

### MySQL Database
- **Container Name**: mysql
- **Database**: wordpress
- **Username**: AlirezaTaaty
- **Password**: alirezapass123
- **Root Password**: rootpass123
- **Data Volume**: db-data
- **Health Check**: Monitors database readiness

### WordPress
- **Container Name**: wordpress
- **Depends on**: MySQL (waits for health check)
- **Data Volume**: wp-data
- **Environment**: Configured to connect to MySQL database

### Nginx
- **Container Name**: nginx
- **Ports**: 80 (HTTP) and 443 (HTTPS)
- **Features**:
  - SSL termination with custom certificates
  - HTTP to HTTPS redirect
  - Reverse proxy to WordPress
  - Custom header (X-Powered-By: "Alireza Taaty")
- **Depends on**: Both MySQL and WordPress services

## Network Configuration

- **Network Name**: wp_net
- **Type**: Bridge (internal)
- **Purpose**: Isolated communication between services

## Data Persistence

- **db-data**: MySQL database files
- **wp-data**: WordPress installation and uploads

## Health Checks

- MySQL service includes a health check using `mysqladmin ping`
- Other services wait for MySQL to be healthy before starting
- WordPress waits for MySQL, Nginx waits for both MySQL and WordPress

## SSL Configuration

The Nginx server is configured with:
- SSL on port 443
- Self-signed certificates (replace with trusted certificates for production)
- Automatic HTTP to HTTPS redirect

## Management Commands

```bash
# Start services
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs

# View specific service logs
docker-compose logs wordpress
docker-compose logs mysql
docker-compose logs nginx

# Stop and remove with volumes
docker-compose down -v
```

## Security Notes

⚠️ **For Production Use**:
- Replace self-signed certificates with trusted SSL certificates
- Change all default passwords
- Use strong database credentials
- Consider adding additional security headers in Nginx
- Regular security updates for all images

## Troubleshooting

1. **Certificate Issues**: Ensure certificates are in `nginx/certs/` directory
2. **Port Conflicts**: Check if ports 80/443 are available
3. **Database Connection**: Verify MySQL health check passes
4. **Permissions**: Ensure proper file permissions on certificates

## Customization

- Modify `nginx/conf.d/wordpress.conf` for Nginx configuration changes
- Update environment variables in `docker-compose.yml` for different credentials
- Replace SSL certificates in `nginx/certs/` for production use

## License

This project is configured for development purposes. Ensure proper licensing for production use.
