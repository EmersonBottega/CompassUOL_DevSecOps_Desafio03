# CompassUOL Desafio03 - Arquitetura para a empresa "Fast Engineering S/A"

# Visão Geral

### Este repositório contém a arquitetura e instruções necessários para solucionar o problema da empresa "Fast Engineering".  
> [!Important]
> - #### A empresa Fast Engineering possui um eCommerce que está crescendo e eles precisam mudar seu ambiemte On-Premise (físico), para um ambiente em nuvem.

#### Solução atual da empresa Fast Engineering:
- 01 servidor para Banco de Dados Mysql (500GB de dados, 10Gb de RAM, 3
Core CPU);

- 01 servidor para a aplicação utilizando REACT – frontend (5GB de dados,
2Gb de RAM, 1 Core CPU);

- 01 servidor de backend com 3 APIs, com o Nginx servindo de balanceador de
carga e que armazena estáticos como fotos e links. (5GB de dados, 4Gb de
RAM, 2 Core CPU);

#### Arquitetura:
![Diagrama on-premise](https://github.com/user-attachments/assets/262783ff-c1e3-4e4e-a0d5-a33602fa57c9)

### Requisitos da empresa:
> [!Warning]
> ### Modernizar o sistema acima para AWS, seguir as melhores práticas de arquitetura em Cloud AWS, e seguir as seguintes diretrizes:

- Ambiente Kubernetes;
- Banco de dados gerenciado (PaaS e Multi AZ);
- Backup de dados;
- Sistema para persistência de objetos (imagens, vídeos etc.);
- Segurança;

> [!Warning]
> ### Antes de migrar para a nova estrutura, é necessário fazer uma migração “lift-and-shift” ou “as-is”, e apenas depois, promover a modificação para a nova estrutura em Kubernetes.

# Solução
## Etapa 1
### Passo 1 - VPC
Configure uma VPC para usar nos serviços da AWS e para ter uma maior segurança. <br>
Contendo:
- 02 subnets públicas.
- 02 subnets privadas.

### Passo 2 - Security Groups
Crie grupos de segurança personalizados para cada serviço que será utilizado, personalizando as regras de entrada e/ou saída.
- Sendo os grupos para: EC2, Load Balancer, RDS, S3

### Passo 3 - Usuário IAM
Crie um usuário IAM para gerar as credenciais da AWS que serão usadas pelo AWS Replication Agent. 
> [!Warning]
> - Configure permissões mínimas necessárias para predefinir controles de permissão.

### Passo 5 - MGN
Para fazer a migração dos servidores de front-end e back-end:
- No ambiente On-premise, instale o agente MGN nos servidores de front-end e back-end.
- Configure os servidores de origem e destino.

