# CompassUOL Desafio03 - Arquitetura para a empresa "Fast Engineering S/A"

# Visão Geral :monocle_face:

### Tecnologias Utilizadas 👩‍💻
- Windows 10 (Sistema Operacional).
- Draw.io (Desenho das arquiteturas).
- AWS (Serviços de Cloud).

### Este repositório contém a arquitetura e instruções necessários para solucionar o problema da empresa "Fast Engineering".  
> [!Important]
> - #### A empresa Fast Engineering possui um eCommerce que está crescendo e eles precisam mudar seu ambiemte On-Premise (físico), para um ambiente em nuvem.

#### Solução atual da empresa Fast Engineering:
- 01 servidor para Banco de Dados Mysql (500GB de dados, 10Gb de RAM, 3
Core CPU).

- 01 servidor para a aplicação utilizando REACT – frontend (5GB de dados,
2Gb de RAM, 1 Core CPU).

- 01 servidor de backend com 3 APIs, com o Nginx servindo de balanceador de
carga e que armazena estáticos como fotos e links. (5GB de dados, 4Gb de
RAM, 2 Core CPU).

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
> - Ambiente Kubernetes.
> - Banco de dados gerenciado (PaaS e Multi AZ).
> - Backup de dados.
> - Sistema para persistência de objetos (imagens, vídeos etc.).
> - Segurança.

# Solução :bulb:
## Etapa 1️⃣
### Arquitetura da Migração 🛠
![Etapa-1 migrar](https://github.com/user-attachments/assets/b7ca7141-c238-4af1-9882-5cdaf245f27a)

### Arquitetura do Ambiente Modernizado 🛠
![Etapa-1 moderno](https://github.com/user-attachments/assets/4e8ab598-b5fd-4b9f-8057-b7c59754f45d)

### Serviços da AWS utilizados ☁

### ![Virtual-private-cloud-VPC_32](https://github.com/user-attachments/assets/76334271-b989-4d60-b748-bf4f6084ebf9) VPC
Configure uma VPC para usar nos serviços da AWS e para ter uma maior segurança. <br>
Contendo:
- 02 subnets públicas.
- 02 subnets privadas.

### ![Arch_AWS-IAM-Identity-Center_32](https://github.com/user-attachments/assets/08cf189c-b38c-4916-96e5-4ec32c10f1e3) Usuário IAM
Crie um usuário IAM para gerar as credenciais da AWS que serão usadas pelo AWS Replication Agent. 
> [!Warning]
> Configure permissões mínimas necessárias para predefinir controles de permissão.

### ![Arch_AWS-Database-Migration-Service_32](https://github.com/user-attachments/assets/fe88b2a6-fffe-4530-b706-1e8b3c045461) SCT/DMS
Use o AWS SCT para migrar o esquema e ajustar os scripts SQL para que sejam compatíveis com o banco de destino. Após a conversão e ajuste dos esquemas, use o DMS para migrar os dados (e opcionalmente mantê-los sincronizados).

### ![Res_Amazon-Aurora-MySQL-Instance_48](https://github.com/user-attachments/assets/2787e9de-6725-4d28-8d16-d87805fc2239) RDS (MySql)
Criar um bando de dados relacional para usar na migração e para a aplicação.
- Crie um banco relacional MySql.
- Habilite Multi-AZ.
- Habilite backup automático.

### ![Res_Amazon-Simple-Storage-Service_S3-Express-One-Zone_48](https://github.com/user-attachments/assets/ee1e769e-0f93-4854-8df7-80aa9bebce94) S3
Utilize para armazenar backups e arquivos estáticos da aplicação.

### ![Arch_AWS-Application-Migration-Service_32](https://github.com/user-attachments/assets/15c6c0c9-12fb-4e0d-8e65-91cba6f9fff4) MGN
Para fazer a migração dos servidores de front-end e back-end:
- No ambiente On-premise, instale o agente MGN nos servidores de front-end e back-end.
- Configure os servidores de origem e destino.

> [!Warning]
> Use um Replication Server: Servidores de replicação são instâncias do Amazon EC2 usadas para replicar dados entre os servidores de origem e a AWS.

### ![Arch_Amazon-Elastic-Block-Store_32](https://github.com/user-attachments/assets/af020bc9-9101-4a17-ad40-33e82004433d) EBS
Use para oferecer desempenho consistente e escalabilidade. Os volumes podem ser redimensionados dinamicamente, permitindo que os usuários aumentem ou diminuam a capacidade de armazenamento conforme necessário.

### ![Res_Elastic-Load-Balancing_Application-Load-Balancer_48](https://github.com/user-attachments/assets/6ebfdb84-4166-4824-804c-39354cbebd72) Application Load Balancer
Use o Application Load Balancer para direcionar o tráfego.

> [!Tip]
> #### 03 Estratégias para direcionar o tráfego: 
> - URLs (exemplo: /api/* vai para o backend e / vai para o frontend).
> - Hosts (exemplo: api.example.com para o backend e example.com para o frontend).
> - Cabeçalhos, cookies ou outros parâmetros.

### ![Res_Amazon-EC2_Auto-Scaling_48](https://github.com/user-attachments/assets/0943c9b0-aa9e-4c21-b11a-3447d7b26d79) Templates de EC2 e Auto Scaling Groups
Para os servidores de front-end e back-end, crie duas templates que serão utilizadas no Auto Scaling com o intuito de garantir a escalabilidade automática das instâncias EC2.

- Crie o primeiro template que será do back-end.
> [!Tip]
> Configure na subnet privada.

- Crie o segundo template que será do front-end.
> [!Tip]
> Configure na subnet pública.

- Por último configure o Auto Scaling groups, com as templates criadas acima.
> [!Tip]
> O Auto Scaling groups contém o Application Load Balancer criado anteriormente.

### ![Res_Amazon-Route-53_Readiness-Checks_48](https://github.com/user-attachments/assets/36b62ffe-ec76-4bdd-8285-4403158eb6bf) Route 53
Use para conectar as requisições do usuário à aplicações da Internet executadas na AWS ou on-premises.

### ![Arch_Amazon-CloudWatch_32](https://github.com/user-attachments/assets/bf3a35da-8579-48aa-8517-3a46ad635865) Cloud Watch
Use o CloudWatch para monitorar métricas, logs e desempenho dos recursos AWS, garantindo operação eficiente e identificando problemas da aplicação.

### ![Res_Amazon-Simple-Email-Service_Email_48](https://github.com/user-attachments/assets/caf6965f-ea98-41bf-992f-be3308459792) Simple Email Service
Use na aplicação para automação de e-mails de alto volume.

### ![Arch_AWS-Budgets_32](https://github.com/user-attachments/assets/aa1cd7a7-443d-4f46-a7b4-eb3405f48143) AWS Budgets
Use para definir orçamentos personalizados para rastrear os custos da empresa e para receber alertas via email, como um aviso de que o valor mensal chegou em $2.000,00.

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
### Arquitetura do Ambiente Final pós migração com Kubernetes 🛠
![Etapa-2 final](https://github.com/user-attachments/assets/fe96d308-32b9-4c5d-bad4-96a7e35c508d)

### Serviços da AWS utilizados ☁

### ![Virtual-private-cloud-VPC_32](https://github.com/user-attachments/assets/76334271-b989-4d60-b748-bf4f6084ebf9) VPC
Configure uma VPC para usar nos serviços da AWS e para ter uma maior segurança. <br>
Contendo:
- 02 subnets públicas.
- 02 subnets privadas.

### ![Arch_AWS-IAM-Identity-Center_32](https://github.com/user-attachments/assets/08cf189c-b38c-4916-96e5-4ec32c10f1e3) Usuário IAM
Crie um usuário IAM para gerar as credenciais da AWS que serão usadas pelo AWS Replication Agent. 
> [!Warning]
> Configure permissões mínimas necessárias para predefinir controles de permissão.

### ![Res_Amazon-Aurora-MySQL-Instance_48](https://github.com/user-attachments/assets/2787e9de-6725-4d28-8d16-d87805fc2239) RDS (MySql)
Criar um bando de dados relacional para usar na migração e para a aplicação.
- Crie um banco relacional MySql.
- Habilite Multi-AZ.
- Habilite backup automático.

### ![Res_Elastic-Load-Balancing_Application-Load-Balancer_48](https://github.com/user-attachments/assets/6ebfdb84-4166-4824-804c-39354cbebd72) Application Load Balancer
Use o Application Load Balancer para direcionamento de tráfego.

> [!Tip]
> #### 03 Estratégias para direcionar o tráfego: 
> - URLs (exemplo: /api/* vai para o backend e / vai para o frontend).
> - Hosts (exemplo: api.example.com para o backend e example.com para o frontend).
> - Cabeçalhos, cookies ou outros parâmetros.

### ![Res_Amazon-Elastic-Container-Registry_Registry_48](https://github.com/user-attachments/assets/ac3cd11a-3d82-44d0-aa5c-be61d95e5a81) ECR
Utilize como mediador para fazer a migração dos servidores front-end e back-end para o AWS EKS.

### ![Res_Amazon-Elastic-Kubernetes-Service_EKS-on-Outposts_48](https://github.com/user-attachments/assets/dd02f9d4-ba34-41e7-8583-8db7019650b7) Elastic Kubernetes Service (EKS)
Para um ambiente com Kubernetes:
- Implemente policies no Kubernetes.
- Configurar Network Policies e Secrets.

### ![Res_Amazon-Route-53_Readiness-Checks_48](https://github.com/user-attachments/assets/36b62ffe-ec76-4bdd-8285-4403158eb6bf) Route 53
Use para conectar as requisições do usuário à aplicações da Internet executadas na AWS ou on-premises.

### ![Arch_Amazon-CloudFront_32](https://github.com/user-attachments/assets/0c5d0c63-9686-4209-a990-e61f86742a54) CloudFront
Use para distribuir conteúdo com segurança, com baixa latência e altas velocidades de transferência.

### ![Arch_AWS-WAF_32](https://github.com/user-attachments/assets/98811761-9734-4d47-9a65-0fe44bb2a925) WAF
Use para criar regras de segurança que controlam o tráfego de bots e bloqueiam padrões de ataque comuns, como injeção de SQL ou cross-site scripting (XSS).

### ![Arch_Amazon-CloudWatch_32](https://github.com/user-attachments/assets/bf3a35da-8579-48aa-8517-3a46ad635865) Cloud Watch
Use o CloudWatch para monitorar métricas, logs e desempenho dos recursos AWS, garantindo operação eficiente e identificando problemas da aplicação.

### ![Arch_Amazon-GuardDuty_32](https://github.com/user-attachments/assets/6d04358a-a2ec-4e6c-8774-15171a28addb) Guard Duty
Use para analisar todo o ambiente AWS em busca de possíveis ameaças, como IP's maliciosos.

### ![Res_Amazon-Simple-Email-Service_Email_48](https://github.com/user-attachments/assets/caf6965f-ea98-41bf-992f-be3308459792) Simple Email Service
Use na aplicação para automação de e-mails de alto volume.

### ![Arch_AWS-Budgets_32](https://github.com/user-attachments/assets/aa1cd7a7-443d-4f46-a7b4-eb3405f48143) AWS Budgets
Use para definir orçamentos personalizados para rastrear os custos da empresa e para receber alertas via email, como um aviso de que o valor mensal chegou em $2.000,00.

### ![terraform](https://github.com/user-attachments/assets/2c7037a2-850e-4388-8344-c24354557d10) Terraform
Use para automatizar uma infraestrutura completa que abrange várias nuvens públicas e privadas, o Terraform também usa arquivos de estado para armazenar informações sobre seus componentes de infraestrutura.

### Requisitos de Segurança:
- VPC com subnets dedicadas ao EKS.
- Configuração de ingressos com SSL no Load Balancer.
- Policies de Kubernetes e monitoramento com CloudWatch e GuardDuty.
- Terraform para aumentar a segurança da infraestrutura.

### Processo de Backup:
- Snapshots automáticos de RDS e EBS.
- Configuração de backup de volumes persistentes.

## Preços de acordo com a AWS Pricing Calculator 💲

![image](https://github.com/user-attachments/assets/18be134a-b75a-4f35-8d14-cf8a614a91ec)

<b>Detalhes:</b> Substituindo as instâncias EC2 por um cluster Kubernetes utilizando o EKS combinado com o ECR. As máquinas, como RDS, e os serviços de armazenamento EBS e S3, permanecem iguais aos configurados no ambiente anterior. Agora, também foi adicionada a integração com o GuardDuty para reforçar a segurança.
