# CompassUOL Desafio03 - Arquitetura para a empresa "Fast Engineering S/A"

# Visão Geral

### Este repositório contém a arquitetura e instruções necessários para solucionar o problema da empresa "Fast Engineering".  
> [!Important]
> - #### A empresa Fast Engineering possui um eCommerce que está crescendo e eles precisam mudar seu ambiemte On-Premise para um ambiente em nuvem como AWS.

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

# Solução
