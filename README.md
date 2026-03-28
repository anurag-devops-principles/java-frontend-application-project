# Java Frontend Application Project 🚀

A complete full-stack Java web application with automated infrastructure deployment and CI/CD pipelines. This project demonstrates modern DevOps practices for deploying Java applications on Azure infrastructure.

![Java Application](frontend/image.png)

## 📋 Overview

This project showcases a complete Java web application deployment pipeline, featuring:

- **Java Web Application**: A responsive login showcase built with HTML/CSS/JavaScript and deployed as a WAR file
- **Azure Infrastructure**: Automated provisioning of Ubuntu VM with Apache Tomcat using Terraform
- **CI/CD Pipelines**: Azure DevOps and GitHub Actions workflows for automated build and deployment
- **Infrastructure as Code**: Complete Azure resource management with Terraform

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   GitHub Repo   │───▶│  Azure DevOps   │───▶│   Azure VM      │
│                 │    │   Pipeline      │    │                 │
│ • Java Source   │    │                 │    │ • Ubuntu 22.04  │
│ • Maven Build   │    │ • Maven Build   │    │ • Apache Tomcat │
│ • WAR Artifact  │    │ • Artifact Pub  │    │ • Java 17       │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │                       │
         ▼                       ▼                       ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  GitHub Actions │───▶│   Terraform     │───▶│   User Access   │
│                 │    │   Apply         │    │                 │
│ • Infra Plan    │    │                 │    │ http://vm-ip:8080│
│ • Infra Apply   │    │ • RG, VNet      │    └─────────────────┘
│                 │    │ • VM, Tomcat    │
└─────────────────┘    └─────────────────┘
```

## 📁 Project Structure

```
java-frontend-application-project/
├── frontend/                    # Java web application
│   ├── src/main/webapp/        # Web content (HTML/CSS/JS)
│   ├── pom.xml                 # Maven configuration
│   └── README.md               # Frontend documentation
├── terraform/                  # Infrastructure as Code
│   ├── main.tf                 # Azure resources
│   ├── variables.tf            # Input variables
│   ├── install-tomcat.sh       # VM provisioning script
│   └── README.md               # Infrastructure docs
├── .azure/pipeline/            # Azure DevOps pipelines
│   └── applicationPipeline.yaml
├── .github/workflows/          # GitHub Actions
│   └── terraformWorkflow.yaml
└── README.md                   # This file
```

## 🚀 Features

### Java Application
- **Responsive Login UI**: Modern, mobile-friendly design
- **Bootstrap Framework**: Clean and professional styling
- **Social Login Options**: Facebook, Twitter, Google integration placeholders
- **WAR Deployment**: Standard Java web application packaging

### Infrastructure
- **Ubuntu 22.04 LTS VM**: Latest LTS with long-term support
- **Apache Tomcat 9.0.87**: Production-ready servlet container
- **OpenJDK 17**: Latest LTS Java runtime
- **Azure Networking**: VNet, subnet, NSG, and optional public IP
- **Systemd Integration**: Automatic Tomcat service management

### CI/CD
- **Azure DevOps Pipeline**: Complete build and deploy workflow
- **GitHub Actions**: Infrastructure deployment automation
- **Artifact Management**: Automated WAR file publishing
- **Environment Management**: Staging and production deployments

## 🛠️ Prerequisites

### Development Environment
- **Java 8+**: JDK for compilation
- **Maven 3.6+**: Build automation tool
- **Git**: Version control system

### Infrastructure Deployment
- **Azure Subscription**: Active Azure account
- **Azure CLI**: For authentication
- **Terraform 1.0+**: Infrastructure provisioning

### CI/CD Setup
- **Azure DevOps**: For application pipelines
- **GitHub Repository**: For Actions workflows
- **Self-hosted Agent**: For Azure DevOps pipeline execution

## 🚀 Quick Start

### 1. Clone the Repository
```bash
git clone https://github.com/anurag-devops-principles/java-frontend-application-project.git
cd java-frontend-application-project
```

### 2. Local Development
```bash
# Navigate to frontend directory
cd frontend

# Build the application
mvn clean package

# The WAR file will be created in target/JavaLoginShowcase.war
```

### 3. Infrastructure Deployment
```bash
# Navigate to terraform directory
cd ../terraform

# Initialize Terraform
terraform init

# Plan the deployment
terraform plan

# Apply the configuration
terraform apply
```

### 4. Access the Application
Once deployed, access the application at:
```
http://<vm-public-ip>:8080/JavaLoginShowcase
```

## 📋 Detailed Setup

### Java Application Setup

1. **Build Requirements**:
   ```bash
   # Verify Java and Maven
   java --version
   mvn --version
   ```

2. **Local Development**:
   ```bash
   cd frontend
   mvn clean compile
   ```

3. **Package for Deployment**:
   ```bash
   mvn clean package
   ```

### Infrastructure Setup

1. **Azure Authentication**:
   ```bash
   az login
   az account set --subscription "your-subscription-id"
   ```

2. **Terraform Configuration**:
   ```bash
   cd terraform
   terraform init
   terraform plan -var-file="terraform.tfvars"
   terraform apply -var-file="terraform.tfvars"
   ```

3. **VM Access**:
   ```bash
   # SSH into the VM (replace with actual IP)
   ssh rootadmin@<vm-public-ip>
   ```

### CI/CD Setup

#### Azure DevOps Pipeline
1. **Import Pipeline**: Use `.azure/pipeline/applicationPipeline.yaml`
2. **Configure Environment**: Create VM environment in Azure DevOps
3. **Set Variables**: Configure pipeline variables for deployment

#### GitHub Actions
1. **Configure Secrets**: Add Azure credentials to repository secrets
2. **Workflow Dispatch**: Manual trigger for infrastructure deployment
3. **Reusable Workflows**: Leverages shared workflow library

## 🔧 Configuration

### Application Configuration
- **Context Path**: `/JavaLoginShowcase`
- **Port**: 8080 (Tomcat default)
- **Java Version**: 17 (OpenJDK)

### Infrastructure Variables
```hcl
# terraform/terraform.tfvars
name            = "tomcat"
location        = "centralindia"
enable_pip      = true
vm_password     = "your-secure-password"
subscription_id = "your-subscription-id"
```

### Pipeline Configuration
- **Build Agent**: Self-hosted Ubuntu agent
- **Artifact Name**: `maven-build`
- **Environment**: `java-env-vm-fe`

## 📊 Monitoring & Management

### Tomcat Management
```bash
# On the VM
sudo systemctl status tomcat
sudo systemctl restart tomcat
sudo tail -f /opt/tomcat/logs/catalina.out
```

### Application Logs
```bash
# Tomcat logs location
/opt/tomcat/logs/
├── catalina.out
├── localhost_access_log.txt
└── localhost.log
```

### Infrastructure Monitoring
- **Azure Portal**: Monitor VM metrics and network traffic
- **Resource Group**: `rg-tomcat`
- **VM Name**: `vm-tomcat`

## 🔒 Security Considerations

- **NSG Rules**: Currently allows all inbound TCP (configure for production)
- **VM Password**: Use strong passwords or SSH keys
- **Network Isolation**: Deploy in appropriate subnets
- **Access Control**: Implement proper RBAC for Azure resources

## 🤝 Contributing

1. **Fork the repository**
2. **Create a feature branch**: `git checkout -b feature/amazing-feature`
3. **Commit changes**: `git commit -m 'Add amazing feature'`
4. **Push to branch**: `git push origin feature/amazing-feature`
5. **Open a Pull Request**

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- **Anurag Vijay**: For providing the foundation and branding
- **Apache Tomcat**: Reliable Java application server
- **Ubuntu**: Stable Linux distribution
- **Azure**: Cloud infrastructure platform

## 📞 Support

For questions or issues:
- Create an issue in this repository
- Contact the Anurag Vijay team
- Check the documentation in subdirectories

---

**Made with ❤️ by Anurag Vijay**

*Empowering developers with modern DevOps practices and cloud-native solutions*
