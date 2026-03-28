# Azure Tomcat Server Infrastructure

Infrastructure as Code (IaC) for deploying an Apache Tomcat application server on Azure Virtual Machine using Terraform. This configuration creates a complete, production-ready Tomcat environment with automated server setup.

## Overview

This Terraform configuration deploys a fully configured Apache Tomcat server on Ubuntu 22.04 LTS in Azure. The deployment includes:

- **Ubuntu 22.04 LTS VM** with automated Tomcat installation
- **Apache Tomcat 9.0.87** with OpenJDK 17
- **Network infrastructure** with configurable public access
- **Remote state management** for team collaboration
- **Systemd service integration** for Tomcat management

## Architecture

```
Azure Subscription
├── Resource Group (rg-tomcat)
│   ├── Virtual Network (vnet-tomcat)
│   │   └── Subnet (snet-vnet-tomcat: 10.10.10.0/29)
│   ├── Network Security Group (nsg-tomcat)
│   │   └── Security Rule (Allow all inbound TCP)
│   ├── Public IP (pip-tomcat) [Optional]
│   ├── Network Interface (nic-tomcat)
│   └── Linux VM (vm-tomcat)
│       ├── Ubuntu 22.04 LTS
│       ├── Apache Tomcat 9.0.87
│       └── OpenJDK 17
```

## Resources Created

### Core Infrastructure
- **Resource Group**: `rg-tomcat` - Container for all resources
- **Virtual Network**: `vnet-tomcat` - Address space: 10.10.10.0/24
- **Subnet**: `snet-vnet-tomcat` - Address range: 10.10.10.0/29
- **Network Security Group**: `nsg-tomcat` - Currently allows all inbound TCP traffic

### Virtual Machine
- **VM Name**: `vm-tomcat`
- **Size**: Standard_B2s (2 vCPUs, 4 GiB RAM)
- **OS**: Ubuntu 22.04 LTS (Jammy Jellyfish)
- **Admin User**: rootadmin
- **Java**: OpenJDK 17
- **Tomcat**: Version 9.0.87

### Networking
- **Public IP**: `pip-tomcat` (Static allocation, optional)
- **Network Interface**: `nic-tomcat` with dynamic private IP
- **DNS**: Accessible via public IP when enabled

## Prerequisites

### Required Tools
- **Terraform** >= 1.0.0
- **Azure CLI** - For authentication (`az login`)
- **Git** - For repository management

### Azure Permissions
The authenticated user/service principal must have:
- `Contributor` role on the target subscription
- Permission to create VMs, networks, and security groups

### Authentication
```bash
# Login to Azure CLI
az login

# Verify authentication
az account show

# Set target subscription (if needed)
az account set --subscription "bd7b142b-030a-46e3-8a31-579ffb9d2046"
```

## Configuration

### Input Variables

| Variable | Type | Description | Default/Example |
|----------|------|-------------|----------------|
| `name` | string | Base name for all resources | `tomcat` |
| `location` | string | Azure region | `centralindia` |
| `enable_pip` | bool | Enable public IP | `true` |
| `vm_password` | string | VM admin password | `rootadmin@123` |
| `subscription_id` | string | Azure subscription ID | Required |

### Default Configuration

```hcl
name            = "tomcat"
location        = "centralindia"
enable_pip      = true
vm_password     = "rootadmin@123"
subscription_id = "bd7b142b-030a-46e3-8a31-579ffb9d2046"
```

### Resource Naming Convention

All resources follow a consistent naming pattern:
- **Resource Group**: `rg-{name}`
- **VNet**: `vnet-{name}`
- **Subnet**: `snet-vnet-{name}`
- **NSG**: `nsg-{name}`
- **Public IP**: `pip-{name}`
- **NIC**: `nic-{name}`
- **VM**: `vm-{name}`

## Deployment

### Quick Start

1. **Clone the repository**:
   ```bash
   git clone https://github.com/anurag-devops-principles/java-frontend-application-project.git
   cd java-frontend-application-project/terraform
   ```

2. **Initialize Terraform**:
   ```bash
   terraform init
   ```

3. **Review the plan**:
   ```bash
   terraform plan
   ```

4. **Deploy the infrastructure**:
   ```bash
   terraform apply
   ```

### Detailed Deployment Steps

#### Step 1: Authentication
```bash
# Authenticate with Azure
az login

# Verify account
az account show
```

#### Step 2: Configuration
```bash
# Navigate to terraform directory
cd terraform

# Review configuration
cat terraform.tfvars

# Modify variables if needed
# Edit terraform.tfvars with your values
```

#### Step 3: Initialize
```bash
# Download providers and initialize backend
terraform init
```

#### Step 4: Validate
```bash
# Check configuration syntax
terraform validate

# Review planned changes
terraform plan
```

#### Step 5: Deploy
```bash
# Apply the configuration
terraform apply

# Confirm with 'yes' when prompted
```

## Post-Deployment Access

### Tomcat Server
After successful deployment, Tomcat will be accessible at:

- **URL**: `http://<public-ip>:8080`
- **Manager App**: `http://<public-ip>:8080/manager/html`
- **Admin User**: Configurable in Tomcat users configuration

### VM Access
- **SSH**: `ssh rootadmin@<public-ip>`
- **Password**: As configured in `vm_password` variable

### Verification
```bash
# Check Tomcat service status
ssh rootadmin@<public-ip>
sudo systemctl status tomcat

# Check Tomcat logs
sudo journalctl -u tomcat -f

# Verify Java installation
java -version

# Check Tomcat version
/opt/tomcat/bin/version.sh
```

## Tomcat Configuration

### Installation Details
- **Tomcat Version**: 9.0.87
- **Java Version**: OpenJDK 17
- **Installation Directory**: `/opt/tomcat`
- **User**: `tomcat` (system user)
- **Service**: `tomcat` (systemd)

### Service Management
```bash
# Start Tomcat
sudo systemctl start tomcat

# Stop Tomcat
sudo systemctl stop tomcat

# Restart Tomcat
sudo systemctl restart tomcat

# Check status
sudo systemctl status tomcat

# View logs
sudo journalctl -u tomcat -f
```

### Deployment Directory
- **Webapps**: `/opt/tomcat/webapps/`
- **Configuration**: `/opt/tomcat/conf/`
- **Logs**: `/opt/tomcat/logs/`

## Remote State Management

This configuration uses Azure Storage for remote state:
- **Resource Group**: `bootstrapresource-rg`
- **Storage Account**: `bootstrapresourcestrg`
- **Container**: `bootstrapresource-tfstate`
- **State File**: `java.app.terraform.tfstate`

### Benefits
- **Team Collaboration** - Shared state across team members
- **State Locking** - Prevents concurrent modifications
- **Backup** - Automatic state versioning in Azure Storage

## Security Considerations

### Current Configuration
⚠️ **Important**: The default NSG allows all inbound TCP traffic, which is not recommended for production.

### Production Recommendations
1. **Restrict NSG Rules**:
   ```hcl
   security_rule {
     name                       = "allow-tomcat"
     priority                   = 100
     direction                  = "Inbound"
     access                     = "Allow"
     protocol                   = "Tcp"
     source_port_range          = "*"
     destination_port_range     = "8080"
     source_address_prefix      = "YOUR_IP_RANGE"
     destination_address_prefix = "*"
   }
   ```

2. **Use SSH Keys**: Replace password authentication with SSH keys
3. **Enable Azure Monitor**: Add monitoring and alerting
4. **Regular Updates**: Keep Tomcat and Ubuntu updated

### Credentials Management
- Change default VM password before production use
- Use Azure Key Vault for sensitive configuration
- Rotate credentials regularly

## Cost Optimization

### Resource Costs (Approximate)
- **VM (Standard_B2s)**: ~$30-40/month
- **Public IP**: ~$3-5/month (if enabled)
- **Storage**: Minimal cost for remote state

### Cost Saving Tips
- Use B1s VM size for development/testing
- Disable public IP if not needed
- Use Azure Reservations for long-term deployments
- Clean up unused resources with `terraform destroy`

## Troubleshooting

### Common Issues

**VM Provisioning Fails**:
```bash
# Check Azure quotas
az vm list-usage --location centralindia

# Verify subscription limits
az account show
```

**Tomcat Not Starting**:
```bash
# Check service status
sudo systemctl status tomcat

# View detailed logs
sudo journalctl -u tomcat --no-pager | tail -50

# Check Java installation
java -version
```

**Network Connectivity**:
```bash
# Test port accessibility
telnet <public-ip> 8080

# Check firewall rules
sudo ufw status
```

**Terraform State Issues**:
```bash
# Check state file
terraform state list

# Pull latest state
terraform refresh

# Force unlock if needed (use cautiously)
terraform force-unlock LOCK_ID
```

### Logs and Debugging

**Terraform Logs**:
```bash
# Enable debug logging
export TF_LOG=DEBUG
terraform apply
```

**VM Logs**:
```bash
# SSH into VM
ssh rootadmin@<public-ip>

# Check cloud-init logs
sudo cat /var/log/cloud-init-output.log

# Check Tomcat installation logs
sudo cat /var/log/cloud-init.log | grep tomcat
```

## Customization

### Modifying Tomcat Version
Edit `install-tomcat.sh`:
```bash
TOMCAT_VERSION=9.0.87  # Change version here
```

### Adding Custom Configuration
Extend the `install-tomcat.sh` script with additional setup commands.

### VM Size Modification
Change the VM size in `main.tf`:
```hcl
size = "Standard_B2s"  # Change to desired size
```

## Cleanup

### Destroy Infrastructure
```bash
# Destroy all resources
terraform destroy

# Confirm destruction when prompted
```

### Manual Cleanup
If Terraform destroy fails:
1. Delete VM first
2. Remove associated resources (NIC, disks, public IP)
3. Delete resource group

## Integration with Java Applications

### Deploying WAR Files
```bash
# Copy WAR file to VM
scp myapp.war rootadmin@<public-ip>:~

# Deploy to Tomcat
ssh rootadmin@<public-ip>
sudo cp myapp.war /opt/tomcat/webapps/
sudo systemctl restart tomcat
```

### Application Configuration
- **Context Path**: Accessible at `http://<public-ip>:8080/myapp/`
- **Manager Access**: Configure users in `/opt/tomcat/conf/tomcat-users.xml`

## Contributing

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/enhanced-security`
3. **Make changes** following Terraform best practices
4. **Test locally**: `terraform plan` and `terraform apply`
5. **Update documentation** if needed
6. **Commit changes**: `git commit -m 'Add security enhancements'`
7. **Push and create PR**

### Guidelines
- Follow Terraform style conventions
- Add appropriate variables for customization
- Update README for significant changes
- Test in non-production subscription

## Related Projects

- [Azure WebApp IaC Todo Project](https://github.com/anurag-devops-principles/azure-webapp-iac-todo-fullstack-project)
- [Bootstrap Azure Resources](https://github.com/anurag-devops-principles/bootstrap-azure-resources-terraform)
- [DevOps CI/CD Library](https://github.com/anurag-devops-principles/devops-cicd-workflows-pipeline-library)

## License

This project is part of the java-frontend-application-project and follows the same Apache License 2.0.

## Support

For questions or issues:
- Check the main project [README](../README.md)
- Review Azure Terraform documentation
- Contact the infrastructure team

---

**Note**: This configuration provides a quick way to deploy Tomcat on Azure for development and testing. For production use, implement additional security measures and monitoring.
