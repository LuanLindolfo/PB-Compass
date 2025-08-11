# Aplicação do Wordpress em alta-disponibilidade
## Sumário
- [Objetivos](#objetivos)
- [Componentes](#componentes)

# Objetivos
## O presente projeto trata-se de uma aplicação prática de conhecimentos adquiridos na trilha DevSecOps do programa de bolsas da Compass. Dessa forma, a documentação se faz crucial para a aplicação dos conhecimentos adquiridos e do projeto proposto

# Componentes
## VPC
- 2 Subnets públicas
- 4 Subnets privadas
- Route Tables, IGW e NAT Gateway
  
## RDS
- Instância com MySQL
- Segurança com grupo de segurança e subnets privadas

## EFS
- Sistema de arquivos montado nas instâncias EC2 via user-data

## Load Balancer
- Distribuição entre as instâncias EC2

## Auto Scaling Group
- Script de bootstrap (user-data)
- Escalamento baseado em CPU

# Funcionalidade - VPC
Cada componente tem uma atribuição dentro do contexto geral da aplicação, são conceituados por seu encaixe de desempenho, sendo aplicados na região do Norte da Virgínia (Estados Unidos). A VPC fornece a network da aplicação, a área de trabalho onde toda a aplicação ocorrerá, tendo na composição o endereçamento em 10.0.0.0/16 fornecendo 65.536 endereços IPv4 com dois octetos fixos. Há também, em cada zona de disponibilidade da região (AZs), as 6 subredes (4 privadas e 2 públicas) que atuam nessa network com um endereçamento IP com 3 octetos fixos dando suporte de 256 IPs para cada uma, sendo 10.0.1.0/24 e 10.0.4.0/24 para as públicas e 10.0.2.0/24, 10.0.3.0/24, 10.0.7.0/24 e 10.0.8.0/24 para as privadas, dando expansão de endereço para cada uma subrede.

A tabela de rotas é o fator principal para conceituar uma subrede como pública ou privada, dessa forma, são criadas duas tabelas de rotas onde a pública conta com a rota local (destino 10.0.0.0/16) e a rota de acesso à internet (tratando-se do internet gateway associado à VPC que fornece o acesso à internet com identificação de ida e na volta da requisição), enquanto a rota privada conta com a rota local e a rota do NAT gateway que é associado à internet por meio da subrede pública com um IP elástico (ip fixo e ip público) tendo gerenciamento simplificado garantindo que o NAT Gateway tenha um endereço IP público estável e persistente, onde não terá o reconhcimento acessível no retorno de identificação da requisição, logo, a subrede privada fica segura.

# Funcionalidade - RDS
O RDS trata-se de um serviço de banco de dados relacional gerido para SQL (PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, DB2). Neste serviço, há a possibilidade de organização de dados de forma estruturada facilitando a compreensão e o gerenciamento, pesquisa agilizada com índices para consultar e pesquisar dados de forma eficiente e rápida, há também o relacionamento e a otimização onde a relação de dados garamnte consistência e integridade de referências e os própositos dos bancos de dados são otimizados adequando-se às necessidades da aplicação.
