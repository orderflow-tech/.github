# OrderFlow - Sistema de Gerenciamento de Pedidos

Sistema completo de gerenciamento de pedidos para lanchonetes, desenvolvido com arquitetura de microsserviços, utilizando AWS EKS, Lambda, RDS e Cognito.

## Visão Geral

O **OrderFlow** é uma solução moderna e escalável para gerenciamento de pedidos em lanchonetes, implementando as melhores práticas de desenvolvimento, arquitetura limpa, infraestrutura como código e CI/CD automatizado.

### Características Principais

- ✅ **Arquitetura de Microsserviços**: Separação clara de responsabilidades
- ✅ **Autenticação Serverless**: AWS Lambda + Cognito para autenticação via CPF
- ✅ **Infraestrutura como Código**: Terraform para toda a infraestrutura
- ✅ **CI/CD Automatizado**: GitHub Actions para deploy contínuo
- ✅ **Alta Disponibilidade**: Multi-AZ deployment no EKS
- ✅ **Escalabilidade Automática**: HPA e Cluster Autoscaler
- ✅ **Segurança**: Secrets Manager, Network Policies, RBAC
- ✅ **Monitoramento**: CloudWatch, Performance Insights
- ✅ **Clean Architecture**: Separação em camadas bem definidas

## Arquitetura do Sistema

```
┌─────────────────────────────────────────────────────────────────┐
│                         Cliente (Frontend)                       │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      API Gateway + Lambda                        │
│                  (Autenticação via CPF + JWT)                    │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Application Load Balancer                     │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      Kubernetes (AWS EKS)                        │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │              OrderFlow Application Pods                    │  │
│  │  (Node.js + TypeScript + Clean Architecture)              │  │
│  └───────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   PostgreSQL (AWS RDS)                           │
│              (Banco de Dados Gerenciável)                        │
└─────────────────────────────────────────────────────────────────┘
```

## Estrutura do Projeto

O projeto está organizado em **4 repositórios/pastas independentes**, cada um com sua própria infraestrutura, CI/CD e responsabilidades:

```
OrderFlow-Project/
├── orderflow-lambda-auth/          # Função Lambda de autenticação
│   ├── src/                        # Código da função
│   ├── terraform/                  # Infraestrutura (Lambda, Cognito, API Gateway)
│   ├── .github/workflows/          # CI/CD
│   └── README.md
│
├── orderflow-infra-kubernetes/     # Infraestrutura Kubernetes
│   ├── terraform/                  # Infraestrutura (EKS, VPC, IAM)
│   ├── .github/workflows/          # CI/CD
│   └── README.md
│
├── orderflow-infra-database/       # Infraestrutura do Banco de Dados
│   ├── terraform/                  # Infraestrutura (RDS, VPC, Security Groups)
│   ├── migrations/                 # Scripts de migração SQL
│   ├── .github/workflows/          # CI/CD
│   └── README.md
│
└── orderflow-application/          # Aplicação Principal
    ├── src/                        # Código da aplicação
    ├── k8s/                        # Manifestos Kubernetes
    ├── .github/workflows/          # CI/CD
    ├── Dockerfile
    └── README.md
```

### 1. orderflow-lambda-auth

**Responsabilidade**: Autenticação de clientes via CPF

**Tecnologias**:
- AWS Lambda (Node.js 20.x)
- AWS Cognito
- API Gateway
- Secrets Manager

**Funcionalidades**:
- Validação de CPF
- Criação automática de usuários no Cognito
- Geração de tokens JWT
- Integração com API Gateway

[📖 Documentação Completa](https://github.com/orderflow-tech/orderflow-lambda-auth/blob/main/README.md)

### 2. orderflow-infra-kubernetes

**Responsabilidade**: Infraestrutura do cluster Kubernetes

**Tecnologias**:
- AWS EKS
- VPC com subnets públicas e privadas
- IAM Roles e Policies
- Security Groups
- Helm (Load Balancer Controller, Metrics Server)

**Recursos Provisionados**:
- Cluster EKS com versão configurável
- Node Groups com auto scaling
- Load Balancer Controller
- Cluster Autoscaler
- Metrics Server

[📖 Documentação Completa](https://github.com/orderflow-tech/orderflow-infra-kubernetes/blob/main/README.md)

### 3. orderflow-infra-database

**Responsabilidade**: Infraestrutura do banco de dados

**Tecnologias**:
- AWS RDS PostgreSQL 16.x
- VPC dedicada
- Security Groups
- Secrets Manager
- CloudWatch Alarms

**Recursos Provisionados**:
- RDS PostgreSQL com Multi-AZ (produção)
- Backups automáticos
- Enhanced Monitoring
- Performance Insights
- Read Replica (opcional)


[📖 Documentação Completa](https://github.com/orderflow-tech/orderflow-infra-database/blob/main/README.md)

### 4. orderflow-application

**Responsabilidade**: Aplicação principal do sistema

**Tecnologias**:
- Node.js 20.x + TypeScript
- Clean Architecture
- Docker
- Kubernetes

**Funcionalidades**:
- Gerenciamento de clientes
- Catálogo de produtos
- Criação e acompanhamento de pedidos
- Integração com gateway de pagamento
- Webhook de pagamento

[📖 Documentação Completa](https://github.com/orderflow-tech/orderflow-application/blob/main/README.md)

## Pré-requisitos

### Ferramentas Necessárias

- **Node.js** 20.x ou superior
- **Terraform** 1.7.0 ou superior
- **AWS CLI** configurado
- **kubectl** instalado
- **Docker** instalado
- **Git** instalado

### Conta AWS

- Conta AWS ativa
- Permissões para criar recursos (EKS, RDS, Lambda, Cognito, etc.)
- AWS CLI configurado com credenciais

### GitHub

- Conta GitHub
- Repositórios criados para cada componente
- GitHub Actions habilitado

## Guia de Deploy

### 1. Configurar Secrets no GitHub

Para cada repositório, configure os seguintes secrets:

```
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
JWT_SECRET (apenas para lambda-auth)
EKS_CLUSTER_NAME (apenas para application)
ECR_REPOSITORY (apenas para application)
```

### 2. Deploy da Infraestrutura do Banco de Dados

```bash
cd orderflow-infra-database/terraform
terraform init
terraform plan
terraform apply
```

**Outputs importantes**:
- `db_instance_endpoint`: Endpoint do RDS
- `db_credentials_secret_arn`: ARN do secret com credenciais

### 3. Deploy da Infraestrutura Kubernetes

```bash
cd orderflow-infra-kubernetes
terraform init
terraform plan
terraform apply
```

**Outputs importantes**:
- `cluster_name`: Nome do cluster EKS
- `cluster_endpoint`: Endpoint do cluster

### 4. Deploy da Função Lambda de Autenticação

```bash
cd orderflow-lambda-auth
npm install
npm run build
npm run package

cd terraform
terraform init
terraform plan
terraform apply
```

**Outputs importantes**:
- `api_gateway_url`: URL do endpoint de autenticação
- `user_pool_id`: ID do Cognito User Pool

### 5. Deploy da Aplicação

```bash
cd orderflow-application

# Configurar kubectl
aws eks update-kubeconfig --region us-east-1 --name <cluster-name>

# Criar secrets
kubectl create namespace orderflow
kubectl create secret generic db-credentials \
  --from-literal=host=<db-endpoint> \
  --from-literal=port=5432 \
  --from-literal=database=orderflowdb \
  --from-literal=username=orderflow_admin \
  --from-literal=password=<password> \
  -n orderflow

# Deploy
cd k8s
./deploy.sh
```

### 6. Verificar Deploy

```bash
# Verificar pods
kubectl get pods -n orderflow

# Verificar serviços
kubectl get svc -n orderflow

# Verificar ingress
kubectl get ingress -n orderflow

# Obter URL da aplicação
kubectl get ingress orderflow-ingress -n orderflow -o jsonpath='{.status.loadBalancer.ingress[0].hostname}'
```

## CI/CD Automatizado

Todos os repositórios possuem pipelines de CI/CD configurados com GitHub Actions:

### Fluxo de Trabalho

1. **Pull Request**: 
   - Lint e validação
   - Testes
   - Terraform plan
   - Security scan
   - Code review

2. **Merge para develop**:
   - Deploy automático para ambiente de desenvolvimento

3. **Merge para main**:
   - Deploy automático para ambiente de produção
   - Smoke tests
   - Rollback automático em caso de falha

### Proteção de Branches

- ✅ Branch `main` protegida
- ✅ Requer Pull Request para merge
- ✅ Requer aprovação de code review
- ✅ Requer passagem em todos os checks
- ✅ Deploy automático após merge

## Segurança

### Boas Práticas Implementadas

#### Infraestrutura
- ✅ VPCs dedicadas e isoladas
- ✅ Subnets privadas para recursos sensíveis
- ✅ Security Groups restritivos
- ✅ IAM Roles com princípio do menor privilégio
- ✅ Criptografia em repouso e em trânsito
- ✅ Secrets Manager para credenciais

#### Aplicação
- ✅ Autenticação JWT
- ✅ Validação de entrada
- ✅ Network Policies no Kubernetes
- ✅ RBAC configurado
- ✅ Scan de vulnerabilidades no CI/CD
- ✅ Container image scanning

#### CI/CD
- ✅ Secrets armazenados no GitHub Secrets
- ✅ Scan de segurança em cada build
- ✅ Análise estática de código
- ✅ Dependency scanning

## Monitoramento

### CloudWatch

- **Logs**: Centralizados no CloudWatch Logs
- **Métricas**: CPU, memória, rede, disco
- **Alarmes**: Configurados para recursos críticos
- **Container Insights**: Monitoramento do EKS

### Performance Insights

- Habilitado no RDS para análise de queries
- Retenção de 7 dias
- Identificação de queries lentas

### Application Monitoring

```bash
# Logs da aplicação
kubectl logs -f deployment/orderflow-app -n orderflow

# Métricas de recursos
kubectl top pods -n orderflow
kubectl top nodes

# Status do HPA
kubectl get hpa -n orderflow
```

## Custos Estimados

### Ambiente de Desenvolvimento

| Serviço | Custo Mensal (USD) |
|---------|-------------------|
| EKS Control Plane | $73 |
| EC2 Nodes (2x t3.medium) | $60 |
| RDS (db.t3.micro) | $15 |
| NAT Gateway (2x) | $65 |
| Lambda + API Gateway | $5 |
| Load Balancer | $20 |
| **Total** | **~$238** |

### Ambiente de Produção

| Serviço | Custo Mensal (USD) |
|---------|-------------------|
| EKS Control Plane | $73 |
| EC2 Nodes (4x t3.large) | $240 |
| RDS (db.t3.small Multi-AZ) | $60 |
| NAT Gateway (2x) | $65 |
| Lambda + API Gateway | $10 |
| Load Balancer | $20 |
| Read Replica | $30 |
| **Total** | **~$498** |

*Valores aproximados e sujeitos a alterações*

## Troubleshooting

### Problemas Comuns

#### 1. Erro de Autenticação AWS

```bash
# Verificar credenciais
aws sts get-caller-identity

# Reconfigurar
aws configure
```

#### 2. Cluster EKS não acessível

```bash
# Reconfigurar kubectl
aws eks update-kubeconfig --region us-east-1 --name <cluster-name>

# Verificar conectividade
kubectl get nodes
```

#### 3. Pods não iniciam

```bash
# Verificar logs
kubectl logs <pod-name> -n orderflow

# Verificar eventos
kubectl describe pod <pod-name> -n orderflow

# Verificar secrets
kubectl get secrets -n orderflow
```

#### 4. Erro de conexão com banco de dados

- Verificar security groups
- Verificar VPC peering
- Verificar credenciais no secret
- Verificar endpoint do RDS

## Documentação Adicional

- [Documentação Lambda Auth](https://github.com/orderflow-tech/orderflow-lambda-auth/blob/main/README.md)
- [Documentação Infraestrutura Kubernetes](https://github.com/orderflow-tech/orderflow-infra-kubernetes/blob/main/README.md)
- [Documentação Infraestrutura Database](https://github.com/orderflow-tech/orderflow-infra-database/blob/main/README.md)
- [Documentação Aplicação](https://github.com/orderflow-tech/orderflow-application/blob/main/README.md)

## Contribuindo

### Fluxo de Contribuição

1. Fork o repositório
2. Crie uma branch a partir de `develop`
3. Faça suas alterações
4. Execute testes e linter
5. Commit seguindo [Conventional Commits](https://www.conventionalcommits.org/)
6. Abra um Pull Request para `develop`
7. Aguarde code review e aprovação

### Padrões de Código

- **TypeScript**: Tipagem estrita
- **ESLint**: Configuração padrão
- **Prettier**: Formatação automática
- **Testes**: Cobertura mínima de 80%

### Padrões de Commit

```
feat: adiciona nova funcionalidade
fix: corrige bug
docs: atualiza documentação
test: adiciona testes
refactor: refatora código
chore: tarefas de manutenção
```

## Roadmap

### Fase 1 - Concluída ✅
- [x] Infraestrutura base (EKS, RDS, Lambda)
- [x] Autenticação via CPF
- [x] CRUD de clientes, produtos e pedidos
- [x] Integração com gateway de pagamento
- [x] CI/CD automatizado

### Fase 2 - Em Planejamento
- [ ] Dashboard administrativo
- [ ] Relatórios e analytics
- [ ] Notificações push
- [ ] Integração com sistemas de delivery
- [ ] App mobile

### Fase 3 - Futuro
- [ ] Machine Learning para recomendações
- [ ] Sistema de fidelidade
- [ ] Multi-tenancy
- [ ] Internacionalização

## Licença

MIT

## Suporte

Para questões e suporte:
- Abra uma issue no repositório correspondente
- Entre em contato com a equipe de desenvolvimento

## Equipe

Desenvolvido pela equipe OrderFlow

---

**Última atualização**: 01/10/2025  
**Versão**: 1.0.0
