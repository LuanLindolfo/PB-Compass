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
Cada componente tem uma função específica dentro do contexto geral da aplicação, sendo definidos por sua adequação de desempenho. Eles são implantados na região Norte da Virgínia (Estados Unidos).

A VPC fornece a rede da aplicação, o ambiente onde toda a operação ocorre. Seu endereçamento é 10.0.0.0/16, fornecendo 65.536 endereços IPv4 com dois octetos fixos. Em cada zona de disponibilidade (AZ) da região, existem 6 sub-redes (4 privadas e 2 públicas) operando nessa rede. Cada sub-rede usa um endereçamento IP com três octetos fixos, suportando 256 IPs:

- Públicas: 10.0.1.0/24 e 10.0.4.0/24

- Privadas: 10.0.2.0/24, 10.0.3.0/24, 10.0.7.0/24 e 10.0.8.0/24.

A tabela de rotas é o elemento principal que define uma sub-rede como pública ou privada. Para isso, são criadas duas tabelas de rotas:

- Pública: Contém a rota local (destino 10.0.0.0/16) e a rota de acesso à internet (via Internet Gateway associado à VPC, que fornece conectividade bidirecional com a internet, identificando origem e destino das requisições).

- Privada: Contém a rota local e a rota para o NAT Gateway. Este, por sua vez, é associado à internet através de uma sub-rede pública e utiliza um IP elástico (IP público fixo). Essa configuração oferece gerenciamento simplificado, garantindo que o NAT Gateway tenha um endereço IP público estável e persistente. Como o retorno do tráfego não é diretamente acessível (apenas o NAT Gateway conhece o destino interno), as sub-redes privadas permanecem seguras.

# Funcionalidade - RDS
O RDS é um serviço gerenciado de banco de dados relacional que suporta SQL (PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, DB2). Este serviço permite:

1. Organização estruturada de dados: Facilita a compreensão e o gerenciamento das informações.

2. Pesquisa ágil: Utiliza índices para consultas rápidas e eficientes.

3. Relacionamento e integridade: Garante consistência e integridade referencial dos dados através de relações definidas.

4. Otimização: Adequa os recursos e configurações do banco de dados às necessidades específicas da aplicação.

# Funcionalidade - EFS
O EFS é um serviço de armazenamento em rede totalmente gerenciado e Multi-AZ (implantado em múltiplas zonas de disponibilidade dentro da mesma região). Ele utiliza o protocolo NFSv4, montado no diretório /opt/data das instâncias EC2.

Sua arquitetura garante alta disponibilidade:

1. Funcionalidade distribuída: Opera em várias zonas de disponibilidade simultaneamente.

2. Tolerância a falhas: Se uma instância EC2 falhar, as demais mantêm acesso ininterrupto aos arquivos no EFS.

3. Independência de instâncias: O sistema de arquivos não depende do estado de instâncias individuais para funcionar — permanece disponível mesmo que todas as instâncias associadas sejam desativadas.

## NFS
O NFS é o protocolo utilizado pelo EFS. Nesse modelo, o NFS define um serviço como servidor, nesse caso, o EFS atua como servidor (fonte central dos arquivos), enquanto as instâncias EC2 funcionam como clientes que acessam esses arquivos. Essa arquitetura permite múltiplos acessos simultâneos ao mesmo arquivo, otimizando o acesso em tempo real inclusive durante atualizações. Quando uma instância EC2 monta o EFS via protocolo NFS, ela envia comandos para acessar os arquivos do servidor EFS, disponibilizando-os localmente no sistema, no diretório /mnt/efs.
