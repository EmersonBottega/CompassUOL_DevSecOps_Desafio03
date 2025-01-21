# CompassUOL Desafio03 - Arquitetura para a empresa "Fast Engineering S/A"

# Visão Geral :monocle_face:

### Tecnologias Utilizadas 👩‍💻
- Windows 10 (Sistema Operacional);
- Draw.io (Desenho das arquiteturas);
- AWS (Serviços de Cloud).

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

#### Arquitetura 🛠
![Diagrama on-premise](https://github.com/user-attachments/assets/262783ff-c1e3-4e4e-a0d5-a33602fa57c9)

> [!Important]
> ### Requisitos da empresa ⚠

> [!Warning]
> ### Etapa 1️⃣
> ### Antes de migrar para a nova estrutura, é necessário fazer uma migração “lift-and-shift” ou “as-is”, e apenas depois, promover a modificação para a nova estrutura em Kubernetes.

> [!Warning]
> ### Etapa 2️⃣
> ### Modernizar o sistema acima para AWS, seguindo as melhores práticas de arquitetura em Cloud AWS, e seguir as seguintes diretrizes:
> - Ambiente Kubernetes;
> - Banco de dados gerenciado (PaaS e Multi AZ);
> - Backup de dados;
> - Sistema para persistência de objetos (imagens, vídeos etc.);
> - Segurança.

# Solução :bulb:
## Etapa 1️⃣
### Arquitetura 🛠
![Etapa-1](https://github.com/user-attachments/assets/df1cbdee-8a4f-4e70-8f3c-de5fb04ee304)

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

### Passo 4 - SCT/DMS
Use o AWS SCT para migrar o esquema e ajustar os scripts SQL para que sejam compatíveis com o banco de destino. Após a conversão e ajuste dos esquemas, use o DMS para migrar os dados (e opcionalmente mantê-los sincronizados).

### Passo 5 - RDS (MySql)
Criar um bando de dados relacional para usar na migração e para a aplicação.
- Crie um banco relacional MySql.
- Habilite Multi-AZ.
- Habilite backup automático.

### Passo 6 - S3
Utilize para armazenar backups e arquivos estáticos da aplicação.

### Passo 7 - MGN
Para fazer a migração dos servidores de front-end e back-end:
- No ambiente On-premise, instale o agente MGN nos servidores de front-end e back-end.
- Configure os servidores de origem e destino.

### Passo 8 - EBS
Use para oferecer desempenho consistente e escalabilidade. Os volumes podem ser redimensionados dinamicamente, permitindo que os usuários aumentem ou diminuam a capacidade de armazenamento conforme necessário.

### Passo 9 - Application Load Balancer
Use o Application Load Balancer, pois ele pode direcionar o tráfego para diferentes grupos de Auto Scaling.

> [!Tip]
> #### 03 Estratégias para direcionar o tráfego: 
> - URLs (exemplo: /api/* vai para o backend e / vai para o frontend).
> - Hosts (exemplo: api.example.com para o backend e example.com para o frontend).
> - Cabeçalhos, cookies ou outros parâmetros.

### Passo 10 - Templates de EC2 e Auto Scaling Groups
Para os servidores de front-end e back-end, crie duas templates que serão utilizadas no Auto Scaling com o intuito de garantir a escalabilidade automática das instâncias EC2.

- Crie o primeiro template que será do back-end.
> [!Tip]
> - Configure na subnet privada.

- Crie o segundo template que será do front-end.
> [!Tip]
> - Configure na subnet pública.

- Por último configure dois Auto Scaling groups, um com a template do back-end e o outro com a template do front-end.
> [!Tip]
> - Os dois Auto Scaling groups contém o mesmo Load Balancer.

### Passo 11 - Cloud Watch
Use o CloudWatch para monitorar métricas, logs e desempenho dos recursos AWS, garantindo operação eficiente e identificando problemas da aplicação.

### Passo 12 - AWS Budgets
Use para definir orçamentos personalizados para rastrear os custos da empresa, uso e para receber alertas via email, como um aviso de que o valor mensal chegou em $2.000,00.

### Requisitos de Segurança:
- Configuração de VPC isolada com subnets públicas e privadas.
- Regras de Security Groups limitando acesso.
- Configuração de IAM com permissões mínimas necessárias.

### Processo de Backup:
- Backup automático habilitado no RDS MySQL (snapshots diários e retenção de 7 dias).
- Configuração de ciclo de vida no S3 para retenção e arquivamento de objetos.

## Preços de acordo com a AWS Pricing Calculator 💲

### Custo da migração:

![image](https://github.com/user-attachments/assets/fa279815-e50a-4a66-8062-ef6b2207c46e)

#### Detalhes:
- <b>VPC:</b> Configurada com NAT e IPv4 público, atendendo à arquitetura proposta;
- <b>EC2:</b> Duas instâncias EC2 (Back-end e Front-end) do tipo "t3.medium" (2vCPU, Memory: 4 GiB). Caso seja necessário, você pode optar por aumentar a capacidade conforme sua necessidade;
- <b>RDS:</b> Uma instância RDS para banco de dados MySQL, configurada como "db.t3.large" (vCPU: 2, Memory: 8 GiB) para priorizar economia. Para maior desempenho, recomendamos "db.t3.xlarge" (vCPU: 4, Memory: 16 GiB), que oferece mais memória RAM e CPU;
- <b>S3:</b> Com capacidade de 500 GB;

<b>OBS:</b> O AWS Application Migration Service não possui custo no plano gratuito, que inclui 2.160 horas ou 90 dias de uso contínuo. Entretanto, os custos de recursos criados conforme necessário (como EC2, EBS, etc.) não estão incluídos. Por este motivo, já estamos estimando esses valores.

### Custo para manter ambiente AWS mensalmente:

![image](https://github.com/user-attachments/assets/4cf5e9b9-60e9-4a48-96e6-00efb5e95f79)

<b>OBS:</b> Mantendo os padrões citados na migração, porém adicionando um Application Load Balancer, CloudWatch e Budgets.

## Etapa 2️⃣
### Arquitetura 🛠
![Etapa-2](https://github.com/user-attachments/assets/1abfc13b-b3fd-43cc-8620-e7fc5de0f1f6)

### Passo 1 - VPC
Configure uma VPC para usar nos serviços da AWS e para ter uma maior segurança. <br>
Contendo:
- 02 subnets públicas.
- 02 subnets privadas.

### Passo 2 - Usuário IAM
Crie um usuário IAM para gerar as credenciais da AWS que serão usadas pelo AWS Replication Agent. 
> [!Warning]
> - Configure permissões mínimas necessárias para predefinir controles de permissão.

### Passo 3 - SCT/DMS
Use o AWS SCT para migrar o esquema e ajustar os scripts SQL para que sejam compatíveis com o banco de destino. Após a conversão e ajuste dos esquemas, use o DMS para migrar os dados (e opcionalmente mantê-los sincronizados).

### Passo 4 - RDS (MySql)
Criar um bando de dados relacional para usar na migração e para a aplicação.
- Crie um banco relacional MySql.
- Habilite Multi-AZ.
- Habilite backup automático.

### Passo 5 - S3
Utilize para armazenar backups e arquivos estáticos da aplicação.

### Passo 6 - ECR
Utilize como mediador para fazer a migração dos servidores front-end e back-end para o AWS EKS.

### Passo 7 - MGN
Para fazer a migração dos servidores de front-end e back-end:
- Certifique-se de estar utilizando o ECR.
- No ambiente On-premise, instale o agente MGN nos servidores de front-end e back-end.
- Configure os servidores de origem e destino.

### Passo 8 - Application Load Balancer
Use o Application Load Balancer, pois ele pode direcionar o tráfego para diferentes grupos de Auto Scaling.

> [!Tip]
> #### 03 Estratégias para direcionar o tráfego: 
> - URLs (exemplo: /api/* vai para o backend e / vai para o frontend).
> - Hosts (exemplo: api.example.com para o backend e example.com para o frontend).
> - Cabeçalhos, cookies ou outros parâmetros.

### Passo 9 - EBS
Use para oferecer desempenho consistente e escalabilidade. Os volumes podem ser redimensionados dinamicamente, permitindo que os usuários aumentem ou diminuam a capacidade de armazenamento conforme necessário.

### Passo 10 - Elastic Kubernetes Service
Para um ambiente com Kubernetes:
- Implemente policies no Kubernetes.
- Configurar Network Policies e Secrets.

### Passo 11 - Cloud Watch
Use o CloudWatch para monitorar métricas, logs e desempenho dos recursos AWS, garantindo operação eficiente e identificando problemas da aplicação.

### Passo 12 - Guard Duty
Use para analisar todo o ambiente AWS em busca de possíveis ameaças, como IP's maliciosos.

### Passo 13 - AWS Budgets
Use para definir orçamentos personalizados para rastrear os custos da empresa, uso e para receber alertas via email, como um aviso de que o valor mensal chegou em $2.000,00.

### Requisitos de Segurança:
- VPC com subnets dedicadas ao EKS.
- Configuração de ingressos com SSL no Load Balancer.
- Policies de Kubernetes e monitoramento com CloudWatch.

### Processo de Backup:
- Snapshots automáticos de RDS e EBS.
- Configuração de backup de volumes persistentes.

## Preços de acordo com a AWS Pricing Calculator 💲

![image](https://github.com/user-attachments/assets/18be134a-b75a-4f35-8d14-cf8a614a91ec)

<b>Detalhes:</b> Substituindo as instâncias EC2 por um cluster Kubernetes utilizando o EKS combinado com o ECR. As máquinas, como RDS, e os serviços de armazenamento EBS e S3, permanecem iguais aos configurados no ambiente anterior. Agora, também foi adicionada a integração com o GuardDuty para reforçar a segurança.
