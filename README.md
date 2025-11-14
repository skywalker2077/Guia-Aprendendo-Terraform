# 📘 Guia Completo: Aprendendo Terraform com AWS

![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)

> **Guia prático e completo para dominar Infrastructure as Code com Terraform na AWS**

## 📚 O que você vai aprender

✔️ **Instalação passo a passo** (Linux e Windows)  
✔️ **Provisionamento de EC2, RDS, VPC, ELB**  
✔️ **Integração com Packer e Jenkins**  
✔️ **Autoscaling e Load Balancers**  
✔️ **Módulos reutilizáveis**  
✔️ **Boas práticas de segurança** (IAM, Security Groups)  
✔️ **Todos os comandos essenciais explicados**

**Este guia é totalmente gratuito e voltado para quem quer resultados práticos!**

---

## 📖 Índice

- [Instalação](#-instalação)
  - [Linux](#instalação-no-linux)
  - [Windows](#instalação-no-windows)
- [Configuração Inicial](#-configuração-inicial)
- [Conceitos Fundamentais](#-conceitos-fundamentais)
- [Comandos Essenciais](#-comandos-essenciais)
- [Provisionamento de Recursos AWS](#-provisionamento-de-recursos-aws)
  - [EC2 - Elastic Compute Cloud](#1-ec2---elastic-compute-cloud)
  - [VPC - Virtual Private Cloud](#2-vpc---virtual-private-cloud)
  - [RDS - Relational Database Service](#3-rds---relational-database-service)
  - [ELB - Elastic Load Balancer](#4-elb---elastic-load-balancer)
- [Autoscaling](#-autoscaling)
- [Módulos Reutilizáveis](#-módulos-reutilizáveis)
- [Integração com Packer](#-integração-com-packer)
- [Integração com Jenkins](#-integração-com-jenkins)
- [Boas Práticas de Segurança](#-boas-práticas-de-segurança)
- [Exemplos Práticos](#-exemplos-práticos)
- [Troubleshooting](#-troubleshooting)
- [Recursos Adicionais](#-recursos-adicionais)

---

## 🚀 Instalação

### Instalação no Linux

#### Ubuntu/Debian

```bash
# Adicionar repositório HashiCorp
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# Atualizar e instalar
sudo apt update
sudo apt install terraform

# Verificar instalação
terraform --version
```

#### CentOS/RHEL/Fedora

```bash
# Adicionar repositório HashiCorp
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo

# Instalar Terraform
sudo yum -y install terraform

# Verificar instalação
terraform --version
```

#### Instalação Manual (todas as distribuições)

```bash
# Download da versão mais recente
wget https://releases.hashicorp.com/terraform/1.6.5/terraform_1.6.5_linux_amd64.zip

# Descompactar
unzip terraform_1.6.5_linux_amd64.zip

# Mover para /usr/local/bin
sudo mv terraform /usr/local/bin/

# Verificar instalação
terraform --version
```

### Instalação no Windows

#### Método 1: Chocolatey (Recomendado)

```powershell
# Instalar Chocolatey (se não tiver)
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))

# Instalar Terraform
choco install terraform

# Verificar instalação
terraform --version
```

#### Método 2: Download Manual

1. Acesse https://www.terraform.io/downloads
2. Baixe o arquivo `.zip` para Windows
3. Extraia o arquivo `terraform.exe`
4. Mova para uma pasta no PATH (ex: `C:\terraform\`)
5. Adicione ao PATH do sistema:
   - Painel de Controle → Sistema → Configurações avançadas do sistema
   - Variáveis de Ambiente → PATH → Adicionar `C:\terraform\`
6. Abra um novo terminal e verifique:

```powershell
terraform --version
```

---

## ⚙️ Configuração Inicial

### Configurar AWS CLI

```bash
# Instalar AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Configurar credenciais
aws configure
```

**Informações necessárias:**
- AWS Access Key ID
- AWS Secret Access Key
- Default region (ex: `us-east-1`)
- Default output format (ex: `json`)

### Configurar Provider AWS no Terraform

Crie um arquivo `provider.tf`:

```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  required_version = ">= 1.2.0"
}

provider "aws" {
  region = "us-east-1"
  
  default_tags {
    tags = {
      Environment = "Development"
      ManagedBy   = "Terraform"
      Project     = "MyProject"
    }
  }
}
```

---

## 📚 Conceitos Fundamentais

### 1. Infrastructure as Code (IaC)
Gerenciar infraestrutura através de código versionável e reutilizável.

### 2. Declarativo vs Imperativo
Terraform usa abordagem **declarativa**: você define o estado desejado e o Terraform determina como alcançá-lo.

### 3. State File (`terraform.tfstate`)
Arquivo que mapeia recursos reais aos configurados. **Nunca edite manualmente!**

### 4. Resources (Recursos)
Componentes de infraestrutura como EC2, S3, VPC.

### 5. Providers
Plugins que permitem interagir com APIs (AWS, Azure, GCP, etc.).

### 6. Variables e Outputs
- **Variables**: Inputs parametrizáveis
- **Outputs**: Valores exportados após apply

### 7. Modules (Módulos)
Conjunto reutilizável de recursos Terraform.

---

## 🔧 Comandos Essenciais

### Comandos Básicos

```bash
# Inicializar diretório Terraform (download de providers)
terraform init

# Validar sintaxe dos arquivos
terraform validate

# Formatar arquivos .tf
terraform fmt

# Planejar mudanças (preview)
terraform plan

# Aplicar mudanças
terraform apply

# Aplicar sem confirmação interativa
terraform apply -auto-approve

# Destruir todos os recursos
terraform destroy

# Mostrar estado atual
terraform show

# Listar recursos no state
terraform state list

# Mostrar detalhes de um recurso específico
terraform state show aws_instance.example
```

### Comandos de Workspace

```bash
# Listar workspaces
terraform workspace list

# Criar novo workspace
terraform workspace new dev

# Mudar para outro workspace
terraform workspace select prod

# Mostrar workspace atual
terraform workspace show
```

### Comandos de Import

```bash
# Importar recurso existente para o state
terraform import aws_instance.example i-1234567890abcdef0
```

### Comandos de Output

```bash
# Mostrar todos os outputs
terraform output

# Mostrar output específico
terraform output instance_ip
```

### Comandos de Debug

```bash
# Ativar logs detalhados
export TF_LOG=DEBUG
terraform apply

# Salvar logs em arquivo
export TF_LOG_PATH=./terraform.log
```

---

## ☁️ Provisionamento de Recursos AWS

### 1. EC2 - Elastic Compute Cloud

#### Exemplo Básico

```hcl
# ec2.tf
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"  # Amazon Linux 2
  instance_type = "t2.micro"
  
  tags = {
    Name = "WebServer"
  }
}

output "instance_public_ip" {
  value = aws_instance.web_server.public_ip
}
```

#### Exemplo Avançado com User Data

```hcl
resource "aws_instance" "web_server" {
  ami                    = "ami-0c55b159cbfafe1f0"
  instance_type          = "t2.micro"
  key_name               = aws_key_pair.deployer.key_name
  vpc_security_group_ids = [aws_security_group.web.id]
  subnet_id              = aws_subnet.public.id
  
  user_data = <<-EOF
              #!/bin/bash
              yum update -y
              yum install -y httpd
              systemctl start httpd
              systemctl enable httpd
              echo "<h1>Hello from Terraform!</h1>" > /var/www/html/index.html
              EOF
  
  tags = {
    Name = "WebServer"
  }
}

# Key Pair para SSH
resource "aws_key_pair" "deployer" {
  key_name   = "deployer-key"
  public_key = file("~/.ssh/id_rsa.pub")
}
```

### 2. VPC - Virtual Private Cloud

#### VPC Completa com Subnets

```hcl
# vpc.tf
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "main-vpc"
  }
}

# Subnet Pública
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true
  
  tags = {
    Name = "public-subnet"
  }
}

# Subnet Privada
resource "aws_subnet" "private" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"
  
  tags = {
    Name = "private-subnet"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
  
  tags = {
    Name = "main-igw"
  }
}

# Route Table para Subnet Pública
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }
  
  tags = {
    Name = "public-rt"
  }
}

# Associação Route Table
resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}

# NAT Gateway para subnet privada
resource "aws_eip" "nat" {
  domain = "vpc"
}

resource "aws_nat_gateway" "main" {
  allocation_id = aws_eip.nat.id
  subnet_id     = aws_subnet.public.id
  
  tags = {
    Name = "main-nat"
  }
}

# Route Table para Subnet Privada
resource "aws_route_table" "private" {
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.main.id
  }
  
  tags = {
    Name = "private-rt"
  }
}

resource "aws_route_table_association" "private" {
  subnet_id      = aws_subnet.private.id
  route_table_id = aws_route_table.private.id
}
```

### 3. RDS - Relational Database Service

```hcl
# rds.tf
resource "aws_db_subnet_group" "main" {
  name       = "main-db-subnet-group"
  subnet_ids = [aws_subnet.private.id, aws_subnet.private_2.id]
  
  tags = {
    Name = "Main DB Subnet Group"
  }
}

resource "aws_db_instance" "main" {
  identifier             = "myapp-db"
  engine                 = "mysql"
  engine_version         = "8.0"
  instance_class         = "db.t3.micro"
  allocated_storage      = 20
  storage_type           = "gp2"
  storage_encrypted      = true
  
  db_name  = "myappdb"
  username = "admin"
  password = var.db_password  # Usar variável para senha
  
  db_subnet_group_name   = aws_db_subnet_group.main.name
  vpc_security_group_ids = [aws_security_group.rds.id]
  
  backup_retention_period = 7
  backup_window          = "03:00-04:00"
  maintenance_window     = "mon:04:00-mon:05:00"
  
  skip_final_snapshot    = true  # Apenas para dev/test
  
  tags = {
    Name = "MyApp Database"
  }
}

# Security Group para RDS
resource "aws_security_group" "rds" {
  name        = "rds-sg"
  description = "Security group for RDS"
  vpc_id      = aws_vpc.main.id
  
  ingress {
    from_port       = 3306
    to_port         = 3306
    protocol        = "tcp"
    security_groups = [aws_security_group.web.id]
  }
  
  tags = {
    Name = "rds-sg"
  }
}

output "db_endpoint" {
  value     = aws_db_instance.main.endpoint
  sensitive = true
}
```

### 4. ELB - Elastic Load Balancer

#### Application Load Balancer (ALB)

```hcl
# alb.tf
resource "aws_lb" "main" {
  name               = "main-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb.id]
  subnets            = [aws_subnet.public.id, aws_subnet.public_2.id]
  
  enable_deletion_protection = false
  
  tags = {
    Name = "main-alb"
  }
}

# Target Group
resource "aws_lb_target_group" "main" {
  name     = "main-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id
  
  health_check {
    enabled             = true
    healthy_threshold   = 2
    interval            = 30
    matcher             = "200"
    path                = "/"
    port                = "traffic-port"
    protocol            = "HTTP"
    timeout             = 5
    unhealthy_threshold = 2
  }
}

# Listener
resource "aws_lb_listener" "main" {
  load_balancer_arn = aws_lb.main.arn
  port              = "80"
  protocol          = "HTTP"
  
  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.main.arn
  }
}

# Security Group para ALB
resource "aws_security_group" "alb" {
  name        = "alb-sg"
  description = "Security group for ALB"
  vpc_id      = aws_vpc.main.id
  
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "alb-sg"
  }
}

output "alb_dns_name" {
  value = aws_lb.main.dns_name
}
```

---

## 📈 Autoscaling

### Auto Scaling Group com Launch Template

```hcl
# autoscaling.tf

# Launch Template
resource "aws_launch_template" "web" {
  name_prefix   = "web-"
  image_id      = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
  
  vpc_security_group_ids = [aws_security_group.web.id]
  
  user_data = base64encode(<<-EOF
              #!/bin/bash
              yum update -y
              yum install -y httpd
              systemctl start httpd
              systemctl enable httpd
              echo "<h1>Instance: $(hostname)</h1>" > /var/www/html/index.html
              EOF
  )
  
  tag_specifications {
    resource_type = "instance"
    tags = {
      Name = "Web Server ASG"
    }
  }
}

# Auto Scaling Group
resource "aws_autoscaling_group" "web" {
  name                = "web-asg"
  vpc_zone_identifier = [aws_subnet.public.id, aws_subnet.public_2.id]
  target_group_arns   = [aws_lb_target_group.main.arn]
  health_check_type   = "ELB"
  health_check_grace_period = 300
  
  min_size         = 2
  max_size         = 6
  desired_capacity = 2
  
  launch_template {
    id      = aws_launch_template.web.id
    version = "$Latest"
  }
  
  tag {
    key                 = "Name"
    value               = "Web Server ASG"
    propagate_at_launch = true
  }
}

# Scaling Policy - Scale Up
resource "aws_autoscaling_policy" "scale_up" {
  name                   = "scale-up"
  scaling_adjustment     = 1
  adjustment_type        = "ChangeInCapacity"
  cooldown               = 300
  autoscaling_group_name = aws_autoscaling_group.web.name
}

# CloudWatch Alarm - CPU High
resource "aws_cloudwatch_metric_alarm" "cpu_high" {
  alarm_name          = "cpu-high"
  comparison_operator = "GreaterThanOrEqualToThreshold"
  evaluation_periods  = "2"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = "120"
  statistic           = "Average"
  threshold           = "70"
  
  dimensions = {
    AutoScalingGroupName = aws_autoscaling_group.web.name
  }
  
  alarm_actions = [aws_autoscaling_policy.scale_up.arn]
}

# Scaling Policy - Scale Down
resource "aws_autoscaling_policy" "scale_down" {
  name                   = "scale-down"
  scaling_adjustment     = -1
  adjustment_type        = "ChangeInCapacity"
  cooldown               = 300
  autoscaling_group_name = aws_autoscaling_group.web.name
}

# CloudWatch Alarm - CPU Low
resource "aws_cloudwatch_metric_alarm" "cpu_low" {
  alarm_name          = "cpu-low"
  comparison_operator = "LessThanOrEqualToThreshold"
  evaluation_periods  = "2"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"
  period              = "120"
  statistic           = "Average"
  threshold           = "30"
  
  dimensions = {
    AutoScalingGroupName = aws_autoscaling_group.web.name
  }
  
  alarm_actions = [aws_autoscaling_policy.scale_down.arn]
}
```

---

## 📦 Módulos Reutilizáveis

### Estrutura de Módulos

```
modules/
├── vpc/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── ec2/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
└── rds/
    ├── main.tf
    ├── variables.tf
    └── outputs.tf
```

### Exemplo: Módulo VPC

**modules/vpc/main.tf**
```hcl
resource "aws_vpc" "this" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = var.enable_dns_hostnames
  enable_dns_support   = var.enable_dns_support
  
  tags = merge(
    var.tags,
    {
      Name = var.vpc_name
    }
  )
}

resource "aws_subnet" "public" {
  count                   = length(var.public_subnet_cidrs)
  vpc_id                  = aws_vpc.this.id
  cidr_block              = var.public_subnet_cidrs[count.index]
  availability_zone       = var.azs[count.index]
  map_public_ip_on_launch = true
  
  tags = merge(
    var.tags,
    {
      Name = "${var.vpc_name}-public-${count.index + 1}"
    }
  )
}

resource "aws_internet_gateway" "this" {
  vpc_id = aws_vpc.this.id
  
  tags = merge(
    var.tags,
    {
      Name = "${var.vpc_name}-igw"
    }
  )
}
```

**modules/vpc/variables.tf**
```hcl
variable "vpc_name" {
  description = "Nome da VPC"
  type        = string
}

variable "vpc_cidr" {
  description = "CIDR block da VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "public_subnet_cidrs" {
  description = "Lista de CIDRs para subnets públicas"
  type        = list(string)
}

variable "azs" {
  description = "Lista de Availability Zones"
  type        = list(string)
}

variable "enable_dns_hostnames" {
  description = "Habilitar DNS hostnames"
  type        = bool
  default     = true
}

variable "enable_dns_support" {
  description = "Habilitar DNS support"
  type        = bool
  default     = true
}

variable "tags" {
  description = "Tags para recursos"
  type        = map(string)
  default     = {}
}
```

**modules/vpc/outputs.tf**
```hcl
output "vpc_id" {
  description = "ID da VPC"
  value       = aws_vpc.this.id
}

output "public_subnet_ids" {
  description = "IDs das subnets públicas"
  value       = aws_subnet.public[*].id
}

output "igw_id" {
  description = "ID do Internet Gateway"
  value       = aws_internet_gateway.this.id
}
```

### Usando o Módulo

**main.tf**
```hcl
module "vpc" {
  source = "./modules/vpc"
  
  vpc_name             = "production-vpc"
  vpc_cidr             = "10.0.0.0/16"
  public_subnet_cidrs  = ["10.0.1.0/24", "10.0.2.0/24"]
  azs                  = ["us-east-1a", "us-east-1b"]
  
  tags = {
    Environment = "Production"
    Project     = "MyApp"
  }
}

output "vpc_id" {
  value = module.vpc.vpc_id
}
```

---

## 🖼️ Integração com Packer

### Criar AMI customizada com Packer

**packer-template.json**
```json
{
  "variables": {
    "aws_region": "us-east-1",
    "ami_name": "web-server-{{timestamp}}"
  },
  "builders": [
    {
      "type": "amazon-ebs",
      "region": "{{user `aws_region`}}",
      "source_ami_filter": {
        "filters": {
          "virtualization-type": "hvm",
          "name": "amzn2-ami-hvm-*-x86_64-gp2",
          "root-device-type": "ebs"
        },
        "owners": ["amazon"],
        "most_recent": true
      },
      "instance_type": "t2.micro",
      "ssh_username": "ec2-user",
      "ami_name": "{{user `ami_name`}}"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "inline": [
        "sudo yum update -y",
        "sudo yum install -y httpd",
        "sudo systemctl enable httpd"
      ]
    }
  ]
}
```

**Usar AMI no Terraform**
```hcl
# data.tf
data "aws_ami" "web_server" {
  most_recent = true
  owners      = ["self"]
  
  filter {
    name   = "name"
    values = ["web-server-*"]
  }
}

# ec2.tf
resource "aws_instance" "web" {
  ami           = data.aws_ami.web_server.id
  instance_type = "t2.micro"
  
  tags = {
    Name = "Web Server from Packer AMI"
  }
}
```

**Script de build**
```bash
#!/bin/bash
# build.sh

# Build da AMI com Packer
packer build packer-template.json

# Deploy com Terraform
terraform init
terraform apply -auto-approve
```

---

## 🔄 Integração com Jenkins

### Jenkinsfile para Pipeline Terraform

```groovy
pipeline {
    agent any
    
    parameters {
        choice(
            name: 'ACTION',
            choices: ['plan', 'apply', 'destroy'],
            description: 'Ação do Terraform'
        )
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'staging', 'prod'],
            description: 'Ambiente'
        )
    }
    
    environment {
        AWS_CREDENTIALS = credentials('aws-credentials')
        TF_VERSION = '1.6.5'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Terraform Init') {
            steps {
                sh """
                    cd environments/${params.ENVIRONMENT}
                    terraform init -backend-config=backend.tfvars
                """
            }
        }
        
        stage('Terraform Validate') {
            steps {
                sh """
                    cd environments/${params.ENVIRONMENT}
                    terraform validate
                """
            }
        }
        
        stage('Terraform Plan') {
            steps {
                sh """
                    cd environments/${params.ENVIRONMENT}
                    terraform plan -out=tfplan -var-file=${params.ENVIRONMENT}.tfvars
                """
            }
        }
        
        stage('Approval') {
            when {
                expression { params.ACTION == 'apply' || params.ACTION == 'destroy' }
            }
            steps {
                input message: "Deseja ${params.ACTION} no ambiente ${params.ENVIRONMENT}?", ok: "Sim"
            }
        }
        
        stage('Terraform Apply') {
            when {
                expression { params.ACTION == 'apply' }
            }
            steps {
                sh """
                    cd environments/${params.ENVIRONMENT}
                    terraform apply -auto-approve tfplan
                """
            }
        }
        
        stage('Terraform Destroy') {
            when {
                expression { params.ACTION == 'destroy' }
            }
            steps {
                sh """
                    cd environments/${params.ENVIRONMENT}
                    terraform destroy -auto-approve -var-file=${params.ENVIRONMENT}.tfvars
                """
            }
        }
    }
    
    post {
        always {
            archiveArtifacts artifacts: '**/tfplan', allowEmptyArchive: true
            cleanWs()
        }
        success {
            echo "Pipeline executado com sucesso!"
        }
        failure {
            echo "Pipeline falhou!"
        }
    }
}
```

### Estrutura de Diretórios para Multi-Environment

```
terraform/
├── modules/
│   ├── vpc/
│   ├── ec2/
│   └── rds/
├── environments/
│   ├── dev/
│   │   ├── main.tf
│   │   ├── dev.tfvars
│   │   └── backend.tfvars
│   ├── staging/
│   │   ├── main.tf
│   │   ├── staging.tfvars
│   │   └── backend.tfvars
│   └── prod/
│       ├── main.tf
│       ├── prod.tfvars
│       └── backend.tfvars
└── Jenkinsfile
```

---

## 🔒 Boas Práticas de Segurança

### 1. IAM - Identity and Access Management

#### Políticas com Least Privilege

```hcl
# iam.tf
resource "aws_iam_role" "ec2_role" {
  name = "ec2-app-role"
  
  assume_role_policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Action = "sts:AssumeRole"
        Effect = "Allow"
        Principal = {
          Service = "ec2.amazonaws.com"
        }
      }
    ]
  })
}

resource "aws_iam_role_policy" "ec2_policy" {
  name = "ec2-app-policy"
  role = aws_iam_role.ec2_role.id
  
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect = "Allow"
        Action = [
          "s3:GetObject",
          "s3:PutObject"
        ]
        Resource = "arn:aws:s3:::my-app-bucket/*"
      },
      {
        Effect = "Allow"
        Action = [
          "cloudwatch:PutMetricData"
        ]
        Resource = "*"
      }
    ]
  })
}

resource "aws_iam_instance_profile" "ec2_profile" {
  name = "ec2-app-profile"
  role = aws_iam_role.ec2_role.name
}

# Usar no EC2
resource "aws_instance" "app" {
  ami                  = "ami-0c55b159cbfafe1f0"
  instance_type        = "t2.micro"
  iam_instance_profile = aws_iam_instance_profile.ec2_profile.name
}
```

### 2. Security Groups - Regras Restritivas

```hcl
# security-groups.tf

# Web Server Security Group
resource "aws_security_group" "web" {
  name        = "web-sg"
  description = "Security group for web servers"
  vpc_id      = aws_vpc.main.id
  
  # HTTP apenas do ALB
  ingress {
    description     = "HTTP from ALB"
    from_port       = 80
    to_port         = 80
    protocol        = "tcp"
    security_groups = [aws_security_group.alb.id]
  }
  
  # SSH apenas de IPs específicos
  ingress {
    description = "SSH from trusted IPs"
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["203.0.113.0/24"]  # Seu IP corporativo
  }
  
  # Saída para internet
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "web-sg"
  }
}

# Database Security Group
resource "aws_security_group" "database" {
  name        = "database-sg"
  description = "Security group for RDS"
  vpc_id      = aws_vpc.main.id
  
  # MySQL apenas dos web servers
  ingress {
    description     = "MySQL from web servers"
    from_port       = 3306
    to_port         = 3306
    protocol        = "tcp"
    security_groups = [aws_security_group.web.id]
  }
  
  tags = {
    Name = "database-sg"
  }
}
```

### 3. Gerenciamento de Secrets

#### Usando AWS Secrets Manager

```hcl
# secrets.tf
resource "aws_secretsmanager_secret" "db_password" {
  name                    = "myapp/db/password"
  recovery_window_in_days = 7
}

resource "aws_secretsmanager_secret_version" "db_password" {
  secret_id     = aws_secretsmanager_secret.db_password.id
  secret_string = var.db_password
}

# Usar no RDS
data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = aws_secretsmanager_secret.db_password.id
}

resource "aws_db_instance" "main" {
  identifier = "myapp-db"
  engine     = "mysql"
  username   = "admin"
  password   = data.aws_secretsmanager_secret_version.db_password.secret_string
  # ... outras configurações
}
```

#### Usando Variables Sensíveis

**variables.tf**
```hcl
variable "db_password" {
  description = "Database password"
  type        = string
  sensitive   = true
}
```

**terraform.tfvars** (NUNCA commitar!)
```hcl
db_password = "SuperSecretPassword123!"
```

**.gitignore**
```
# Terraform
*.tfstate
*.tfstate.*
*.tfvars
.terraform/
```

### 4. Backend Remoto com Encryption

```hcl
# backend.tf
terraform {
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
    kms_key_id     = "arn:aws:kms:us-east-1:ACCOUNT_ID:key/KEY_ID"
  }
}

# Criar bucket para state
resource "aws_s3_bucket" "terraform_state" {
  bucket = "my-terraform-state-bucket"
  
  lifecycle {
    prevent_destroy = true
  }
}

resource "aws_s3_bucket_versioning" "terraform_state" {
  bucket = aws_s3_bucket.terraform_state.id
  
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "terraform_state" {
  bucket = aws_s3_bucket.terraform_state.id
  
  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "aws:kms"
      kms_master_key_id = aws_kms_key.terraform_state.arn
    }
  }
}

# DynamoDB para state locking
resource "aws_dynamodb_table" "terraform_state_lock" {
  name           = "terraform-state-lock"
  read_capacity  = 5
  write_capacity = 5
  hash_key       = "LockID"
  
  attribute {
    name = "LockID"
    type = "S"
  }
}
```

### 5. Checklist de Segurança

- [ ] Não commitar arquivos `.tfvars` ou state files
- [ ] Usar backend remoto com encryption
- [ ] Implementar state locking
- [ ] Usar IAM roles ao invés de access keys hardcoded
- [ ] Marcar variáveis sensíveis como `sensitive = true`
- [ ] Restringir Security Groups ao mínimo necessário
- [ ] Habilitar encryption at rest para RDS, S3, EBS
- [ ] Usar SSL/TLS para comunicações
- [ ] Implementar logging e monitoring (CloudTrail, CloudWatch)
- [ ] Revisar permissões IAM regularmente
- [ ] Usar tags para rastreabilidade

---

## 💡 Exemplos Práticos

### Projeto Completo: Web App com 3 Tiers

```
project/
├── main.tf
├── variables.tf
├── outputs.tf
├── provider.tf
├── vpc.tf
├── security-groups.tf
├── alb.tf
├── asg.tf
├── rds.tf
└── terraform.tfvars
```

**main.tf**
```hcl
terraform {
  required_version = ">= 1.2.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  
  backend "s3" {
    bucket         = "myapp-terraform-state"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
  }
}
```

**Complete o projeto usando os exemplos anteriores de VPC, ALB, ASG e RDS.**

---

## 🐛 Troubleshooting

### Problemas Comuns

#### 1. State Lock

**Erro:**
```
Error: Error acquiring the state lock
```

**Solução:**
```bash
# Forçar unlock (use com cuidado!)
terraform force-unlock <LOCK_ID>
```

#### 2. Inconsistência de State

**Erro:**
```
Error: Resource 'X' has been deleted outside of Terraform
```

**Solução:**
```bash
# Atualizar state
terraform refresh

# Ou remover do state e reimportar
terraform state rm aws_instance.example
terraform import aws_instance.example i-1234567890abcdef0
```

#### 3. Dependências Circulares

**Erro:**
```
Error: Cycle: resource 'A' depends on 'B' and vice versa
```

**Solução:**
Refatorar código para quebrar ciclo usando `depends_on` ou separar em diferentes resources.

#### 4. Provider Plugin Errors

**Erro:**
```
Error: Failed to query available provider packages
```

**Solução:**
```bash
# Limpar cache
rm -rf .terraform
terraform init -upgrade
```

#### 5. Validação de Credenciais AWS

**Verificar:**
```bash
aws sts get-caller-identity
```

---

## 📚 Recursos Adicionais

### Documentação Oficial

- [Terraform Documentation](https://www.terraform.io/docs)
- [AWS Provider Documentation](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Terraform Best Practices](https://www.terraform-best-practices.com/)

### Ferramentas Úteis

- **terraform-docs**: Gerar documentação automática
- **tflint**: Linter para Terraform
- **checkov**: Security scanner
- **terragrunt**: DRY Terraform configurations
- **atlantis**: Terraform PR automation

### Instalação de Ferramentas

```bash
# terraform-docs
brew install terraform-docs

# tflint
curl -s https://raw.githubusercontent.com/terraform-linters/tflint/master/install_linux.sh | bash

# checkov
pip install checkov
```

### Comandos Úteis

```bash
# Gerar documentação
terraform-docs markdown table . > README.md

# Lint
tflint

# Security scan
checkov -d .

# Formatar todos os arquivos
terraform fmt -recursive

# Visualizar dependências
terraform graph | dot -Tsvg > graph.svg
```

---

## 🎯 Próximos Passos

1. **Pratique** criando seus próprios módulos
2. **Automatize** com CI/CD (Jenkins, GitLab CI, GitHub Actions)
3. **Explore** Terraform Cloud para collaboration
4. **Aprenda** sobre Sentinel para policy as code
5. **Contribua** com a comunidade open source

---

## 📝 Licença

Este guia é distribuído sob licença MIT. Sinta-se livre para usar, modificar e compartilhar.

---

## 🤝 Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para:

- Reportar bugs
- Sugerir melhorias
- Adicionar novos exemplos
- Corrigir erros

---

## 📧 Contato

Para dúvidas ou sugestões, abra uma issue no repositório.

---

**🚀 Bons estudos e happy terraforming!**
