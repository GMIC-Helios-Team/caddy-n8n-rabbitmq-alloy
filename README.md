![Project Logo](./.github/Helios-logo.png)

# caddy-n8n-rabbitmq-alloy

A powerful stack combining Caddy web server, N8N workflow automation, RabbitMQ message broker, and Grafana Alloy for observability.

[![Docker](https://img.shields.io/badge/Docker-ready-2496ED?style=for-the-badge&logo=docker)](https://www.docker.com/)
[![Caddy](https://img.shields.io/badge/Caddy-http_server-1F88C0?style=for-the-badge&logo=caddy)](https://caddyserver.com/)[![RabbitMQ](https://img.shields.io/badge/RabbitMQ-message_broker-FF6600?style=for-the-badge&logo=rabbitmq)](https://www.rabbitmq.com/)
[![N8N](https://img.shields.io/badge/N8N-Workflow-FF6B6B?style=for-the-badge&logo=n8n)](https://n8n.io/)
[![Grafana Alloy](https://img.shields.io/badge/grafana-observability-F46800?style=for-the-badge&logo=grafana)](https://grafana.com/docs/alloy/latest/)


## 🚀 Quick Start

### Prerequisites

- Docker and Docker Compose installed
- Domain name configured
- Basic understanding of environment variables

## 📝 Configuration Steps

### 1. Clone the Repository

```bash
git clone <repository_url>
cd caddy-n8n-rabbitmq-alloy
```

### 2. Update Environment Variables

Create a `.env` file or update the existing `sample.env` file with the necessary environment variables:

```plaintext

DOMAIN_NAME=yourdomain.com
SUBDOMAIN=subdomain
DATA_FOLDER=/data
GENERIC_TIMEZONE=America/New_York
RABBITMQ_DEFAULT_USER=your_rabbitmq_user
RABBITMQ_DEFAULT_PASS=your_rabbitmq_password
```

Replace the values with your own settings.

### 3. Update Configuration Files

#### Caddyfile

Update the `Caddyfile` with your domain and subdomain names:

```plaintext

rabbitmq.{yourdomain.com} {
    reverse_proxy rabbitmq:15672
}

n8n.{yourdomain.com} {
    reverse_proxy n8n:5678 {
        flush_interval -1
    }
}

otlp.{yourdomain.com} {
    reverse_proxy alloy:12345
}

acme_ca https://acme-v02.api.letsencrypt.org/directory
```

#### Alloy Configuration

Update the Alloy configuration file `config.alloy` with your Grafana Cloud credentials:

```plaintext

otelcol.auth.basic "grafana_cloud" {
    username = "your_grafana_username"
    password = "your_grafana_password"
}
```

### 4. Start the Services

Run the following command to start all the services:

```bash
docker-compose up -d
```

### 5. Verify the Services

- **Caddy** should be running and serving your domains.
- **n8n** should be accessible at `https://n8n.yourdomain.com`.
- **RabbitMQ** management UI should be accessible at `https://rabbitmq.yourdomain.com`.
- **Alloy** should be accessible at `https://otlp.yourdomain.com`.

## 🔧 Setting up n8n

### 1. Access n8n

Open your browser and navigate to `https://n8n.yourdomain.com`.

### 2. Import Flows

#### Option 1: Import from n8n UI

- In n8n, click on the menu icon and select **Import**.
- Import the JSON files located in the `n8n-flows` directory:
  - `SendEmail.json`
  - `ImageGen.json`
  - `Helios_Flows.json`
  - `Get_Top_Images.json`

#### Option 2: Place Flows in n8n Data Directory

Copy the flow files directly into the n8n data directory on your host machine:

```plaintext
// Copy flow files to the following directory
${DATA_FOLDER}/n8n_data
```

## 📊 RabbitMQ Configuration

1. Access RabbitMQ Management:
- Open `https://rabbitmq.yourdomain.com`
- Login with credentials from `.env`
2. Create Queues:
- Create `email` queue
- Create `n8n` queue


## 📁 Directory Structure
```
.
├── alloy/
│   └── config.alloy
├── caddy_config/
│   └── Caddyfile
├── n8n-flows/
│   ├── SendEmail.json
│   ├── ImageGen.json
│   ├── Helios_Flows.json
│   └── Get_Top_Images.json
├── docker-compose.yml
├── .env
└── README.md
```

## Additional Configuration

- Ensure that any required credentials (e.g., for OpenAI, databases, etc.) are set up within n8n after importing the flows.
- Update any environment variables in the `docker-compose.yml` file as needed.

## 🛠 Troubleshooting

- Check the logs of individual services if something doesn't work as expected:

```bash
docker-compose logs [service_name]
```

- Ensure that the domain names and subdomains are correctly configured in your DNS records.


## 🔐 Security Notes
- Change default credentials
- Use strong passwords
- Keep API keys secure
- Regularly update containers

## 📝 Maintenance
### Backup:
```
docker-compose down
tar -czf backup.tar.gz data/
```
### Updates:
```
docker-compose pull
docker-compose up -d
```
