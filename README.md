# Agentage Infrastructure - Docker Swarm on Hetzner Cloud

[![Terraform](https://img.shields.io/badge/Terraform-1.12+-purple.svg)](https://www.terraform.io/)
[![Docker Swarm](https://img.shields.io/badge/Docker-Swarm-blue.svg)](https://docs.docker.com/engine/swarm/)
[![Hetzner Cloud](https://img.shields.io/badge/Hetzner-Cloud-red.svg)](https://www.hetzner.com/cloud)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> **Production-ready infrastructure for Agentage platform. Hosts agentage.io and development environment on Hetzner Cloud with Docker Swarm.**

> ℹ️ **Note**: This infrastructure is based on the [vreshch/infrastructure](https://github.com/vreshch/infrastructure) template.

## ✨ Features

- 🚀 **Multi-Domain Support** - Main site + dev environment + extensible for docs/api
- 🌍 **Environment Isolation** - Separate dev/prod environments
- 🔒 **Secure by Default** - SSL certificates, firewall rules, SSH key auth
- 📊 **Built-in Monitoring** - Traefik dashboard, Swarmpit UI, Dozzle logs
- 💰 **Cost-Effective** - Affordable Hetzner Cloud infrastructure
- 🔧 **Interactive Setup** - Guided configuration with validation
- 🛠️ **Utility Scripts** - SSH keys, password hashing, config validation

## 🏗️ Architecture

```
Local/CI → Terraform → Hetzner Cloud
              ↓
      Docker Swarm Infrastructure
```

**Domains:**
- `agentage.io` - Main platform (Next.js web app)
- `dev.agentage.io` - Development environment

**Future Extensibility:**
- `traefik.agentage.io` - Traefik dashboard
- `docs.agentage.io` - Documentation site
- `api.agentage.io` - API endpoints


**Included Services:**
- **Traefik** - Automatic SSL/TLS and reverse proxy for all domains
- **Swarmpit** - Docker Swarm management UI
- **Dozzle** - Real-time container log viewer
- **Automatic DNS** - Managed via Hetzner DNS API

## 🚀 Quick Start

### Prerequisites

- [Hetzner Cloud](https://www.hetzner.com/cloud) account with API token
- [Hetzner DNS](https://dns.hetzner.com/) account with API token
- Domain `agentage.io` registered and configured in Hetzner DNS
- Terraform >= 1.12 (for local deployment)

### Installation

1. **Clone this repository**

   ```bash
   git clone https://github.com/agentage/infrastructure.git
   cd infrastructure
   ```

2. **Run the interactive setup script**

   ```bash
   # For development environment
   ./scripts/setup-fill-tfvars.sh dev
   
   # For production environment
   ./scripts/setup-fill-tfvars.sh prod
   ```
   
   The script will prompt for:
   - Domain configuration (`agentage.io`, `dev.agentage.io`, etc.)
   - Hetzner Cloud & DNS API tokens
   - DNS Zone ID
   - Server configuration (name, type, location)
   - SSH key paths
   - Admin password (or auto-generate)
   
   It automatically:
   - Validates all inputs
   - Generates bcrypt password hash
   - Base64-encodes credentials
   - Creates `terraform/terraform.{env}.tfvars` with secure permissions (600)

3. **Deploy infrastructure**

   ```bash
   cd terraform
   terraform init
   terraform plan -var-file="terraform.prod.tfvars"
   terraform apply -var-file="terraform.prod.tfvars"
   ```

4. **Access your services** (after 5-10 minutes for DNS + SSL)

   - **Main Platform**: `https://agentage.io`
   - **Dev Environment**: `https://dev.agentage.io`
   - **Management Tools**: Configured during setup

   **Note**: SSL certificates are issued automatically but require DNS propagation first (5-10 min).

## 📁 Repository Structure

```
.
├── terraform/                  # Terraform infrastructure code
│   ├── main.tf                 # Main infrastructure definition
│   ├── variables.tf            # Variable definitions with validation
│   ├── outputs.tf              # Output definitions
│   ├── versions.tf             # Provider and backend configuration
│   └── modules/                # Reusable modules
│       ├── compute/            # Server provisioning module
│       │   ├── main.tf
│       │   ├── variables.tf
│       │   ├── outputs.tf
│       │   └── scripts/        # Server initialization scripts
│       │       ├── init-docker.sh
│       │       ├── init-docker-swarm.sh
│       │       └── deploy-services.sh
│       └── dns/                # DNS management module
│           ├── main.tf
│           ├── variables.tf
│           └── outputs.tf
├── configs/                    # Environment configuration templates
│   ├── template.tfvars         # Master configuration template
│   ├── dev.example.tfvars      # Development environment example
│   └── prod.example.tfvars     # Production environment example
├── scripts/                    # Automation scripts
│   ├── setup-env.sh            # Interactive environment setup
│   ├── setup-fill-tfvars.sh    # Fill tfvars with validation
│   ├── deploy-env.sh           # Multi-environment deployment
│   └── utils/                  # Utility scripts
│       ├── generate-ssh-keys.sh    # SSH key generation
│       ├── generate-password.sh    # Password hash generation
│       └── validate-config.sh      # Configuration validation
├── docs/                       # Documentation
│   ├── quickstart.md           # 5-minute setup guide
│   ├── configuration.md        # Configuration reference
│   ├── deployment.md           # Deployment guide
│   └── troubleshooting.md      # Common issues and solutions
└── README.md                   # This file
```

## 🔧 Configuration

### 📝 Domain Configuration

The infrastructure supports multiple subdomains for different services. See `configs/template.tfvars` for complete configuration options.

### 💻 Server Types

Choose appropriate Hetzner Cloud server types based on your needs. Start small and scale as needed. See [Hetzner Cloud Pricing](https://www.hetzner.com/cloud) for current options.

## 🚀 Usage

### Deploy Development Environment

```bash
# 1. Setup dev configuration
./scripts/setup-fill-tfvars.sh dev

# 2. Validate configuration
./scripts/utils/validate-config.sh terraform/terraform.dev.tfvars

# 3. Deploy
./scripts/deploy-env.sh dev apply --local
```

### Deploy Production Environment

```bash
# 1. Setup prod configuration
./scripts/setup-fill-tfvars.sh prod

# 2. Validate configuration
./scripts/utils/validate-config.sh terraform/terraform.prod.tfvars

# 3. Preview changes
./scripts/deploy-env.sh prod plan --local

# 4. Apply changes
./scripts/deploy-env.sh prod apply --local
```

### Update Infrastructure

```bash
# Modify configuration
nano terraform/terraform.prod.tfvars

# Preview changes
./scripts/deploy-env.sh prod plan --local

# Apply changes
./scripts/deploy-env.sh prod apply --local
```

### Add New Subdomain (e.g., docs.agentage.io)

See [INSTRUCTIONS.md](INSTRUCTIONS.md) for detailed steps on extending the infrastructure.

## 📊 Monitoring & Management

### 🛠️ Built-in Tools

The infrastructure includes management tools for monitoring and controlling your Docker Swarm infrastructure:

- **Traefik Dashboard** - Routing rules, SSL status, service health
- **Swarmpit** - Docker services, logs, resource monitoring
- **Dozzle** - Real-time container logs with search

All tools use basic authentication with configured admin credentials.

## 🔒 Security

- **Firewall**: Only ports 80, 443, and 22 exposed
- **SSL/TLS**: Automatic Let's Encrypt certificates for all domains
- **Authentication**: Admin tools protected with bcrypt hashing
- **SSH**: Key-based authentication only (no passwords)
- **Secrets**: Never committed (.gitignore configured)
- **File Permissions**: tfvars files set to 600 automatically

### Security Best Practices

- Generate unique SSH keys per environment
- Use strong passwords (16+ characters recommended)
- Rotate credentials regularly
- Enable MFA on Hetzner account
- Review firewall rules periodically
- Keep server software updated

## 📝 Documentation

- **[docs/quickstart.md](docs/quickstart.md)** - 5-minute setup guide
- **[docs/configuration.md](docs/configuration.md)** - Complete configuration reference
- **[docs/deployment.md](docs/deployment.md)** - Deployment workflows
- **[docs/troubleshooting.md](docs/troubleshooting.md)** - Common issues and solutions

## 🔧 Troubleshooting

### DNS not resolving

```bash
# Check DNS records
dig +short agentage.io
dig +short dev.agentage.io

# Verify Hetzner DNS zone ID
# Login to https://dns.hetzner.com/
```

### SSL certificates not generating

```bash
# SSH to server and check Traefik logs
ssh -i ~/.ssh/deploy_ed25519 root@YOUR_SERVER_IP
docker service logs traefik

# Common causes:
# - DNS not propagated (wait 5-10 minutes)
# - Port 80/443 not accessible
# - Invalid email address
```

### Services not accessible

```bash
# Check service status
ssh -i ~/.ssh/deploy_ed25519 root@YOUR_SERVER_IP
docker service ls
docker service ps traefik swarmpit dozzle

# View logs
docker service logs <service-name>
```

For detailed troubleshooting, see [docs/troubleshooting.md](docs/troubleshooting.md).

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

> 💡 **Note**: For general infrastructure improvements, consider contributing to the upstream [vreshch/infrastructure](https://github.com/vreshch/infrastructure) template so all users can benefit.

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'feat: add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📞 Support

- **Documentation**: See `docs/` directory for detailed guides
- **Issues**: Report bugs or request features via [GitHub Issues](https://github.com/agentage/infrastructure/issues)
- **Discussions**: Ask questions in [GitHub Discussions](https://github.com/agentage/infrastructure/discussions)

## 🙏 Acknowledgments

- [Dockerswarm.rocks](https://dockerswarm.rocks/) - Docker Swarm best practices
- [Hetzner Cloud](https://www.hetzner.com/cloud) - Affordable cloud infrastructure
- [Traefik](https://traefik.io/) - Modern reverse proxy and load balancer
- [Swarmpit](https://swarmpit.io/) - Docker Swarm management interface
- [Dozzle](https://dozzle.dev/) - Real-time log viewer for Docker

## 📝 License

MIT © 2025 Agentage Contributors

---

**Part of the Agentage ecosystem** © 2025 Agentage GmbH - [agentage.io](https://agentage.io)