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

No grupo de segurança foram implantadas as regras de entrada:
1. Todo o acesso IPv4 com o IP qualquer/IP do host 
2. HTTP na porta 80 com IP qualquer/IP do host
3. HTTPS na porta 443 com IP qualquer/IP do host
4. SSH na porta 22 com IP qualquer/IP do host
5. MYSQL na porta 3306 com IP qualquer/IP do host

É importante ressaltar que o IP qualquer pode ser implantado para a finalidade da agilidade no processo visando a mudança de IP da máquina local, mas para melhor segurança, impôr o IP da máquina de acesso/host se torna a prática de segurança mais importante.

Na regra de saída, é permitido todo o acesso IPv4 com o IP qualquer/IP do host.
# Funcionalidade - RDS
O RDS é um serviço gerenciado de banco de dados relacional que suporta SQL (PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, DB2). Este serviço permite:

1. Organização estruturada de dados: Facilita a compreensão e o gerenciamento das informações.

2. Pesquisa ágil: Utiliza índices para consultas rápidas e eficientes.

3. Relacionamento e integridade: Garante consistência e integridade referencial dos dados através de relações definidas.

4. Otimização: Adequa os recursos e configurações do banco de dados às necessidades específicas da aplicação.

Na aplicação, foi criado um banco de dados MySQL na versão mais recente (8.4.6) no modelo de nível gratuito e consequentemnet a aplicação Single-AZ. O user e a senha foram criados com gerenciamento de credenciais autogerenciado com os recursos dr.t3.micro e armazenamento padrão.
A conectividade do RDS foi selecionado para se conectar à EC2, logo, o RDS irá se conectar aos recursos da EC2 ativa tendo contato direto como desejado na aplicação. O tipo de rede em ipv4 e o grupo de segurança selecionado ao que foi ajustado para a aplicação do banco de dados e com a seleção da zona de disponibilidado de RDS.

O RDS foi aplicado em cada zona de disponibilidade onde se conecta à todas as subredes privadas.

## Grupo de segurança RDS
O grupo de segurança do RDS foi criado para a finalidade de conexão com a EC2 somente ouvindo-a. Logo, o grupo de segurança do RDS tem como regras de entrada:
1. MYSQL na porta 3306 com IP qualquer/IP do host

Na regra de saída, é permitido todo o acesso IPv4 com o IP qualquer/IP do host.
# Funcionalidade - EFS
O EFS é um serviço de armazenamento em rede totalmente gerenciado e Multi-AZ (implantado em múltiplas zonas de disponibilidade dentro da mesma região). Ele utiliza o protocolo NFSv4, montado no diretório /opt/data das instâncias EC2.

Sua arquitetura garante alta disponibilidade:

1. Funcionalidade distribuída: Opera em várias zonas de disponibilidade simultaneamente.

2. Tolerância a falhas: Se uma instância EC2 falhar, as demais mantêm acesso ininterrupto aos arquivos no EFS.

3. Independência de instâncias: O sistema de arquivos não depende do estado de instâncias individuais para funcionar — permanece disponível mesmo que todas as instâncias associadas sejam desativadas.

## NFS
O NFS é o protocolo utilizado pelo EFS. Nesse modelo, o NFS define um serviço como servidor, nesse caso, o EFS atua como servidor (fonte central dos arquivos), enquanto as instâncias EC2 funcionam como clientes que acessam esses arquivos. Essa arquitetura permite múltiplos acessos simultâneos ao mesmo arquivo, otimizando o acesso em tempo real inclusive durante atualizações. Quando uma instância EC2 monta o EFS via protocolo NFS, ela envia comandos para acessar os arquivos do servidor EFS, disponibilizando-os localmente no sistema, no diretório /mnt/efs.

## EFS via user data com protocolo NFS

# Funcionalidade - Load Balancer
O Load Balancer é um serviço que forne o balanceamento de carga entre as aplicações com vantagens aplicadas como o Gerenciamento da AWS (onde a AWS trata a manutenção, upgrades, funcionalidade e a alta disponibilidade), facilidade de Configuração por meio de poucas opções de configuração para simplificar o uso, e custo benefício reduzindo significativamente o esforço de manutenção e integração por parte do usuário. O Load Balancer trata um tópico importante sendo ele: Escabilidade Vertical (aumentando o poder computacional de uma instância) e Horizontal (aumentar o número de instâncias ou sistemas para sua aplicação.)

Há quatro tipos de Load Balancers:
- **Application Load Balancer (ALB):**
    - Opera na **Camada 7 (HTTP / HTTPS)**.
    - Ideal para aplicações web e microsserviços.
      
 - **Network Load Balancer (NLB):**
    - Opera na **Camada 4 (TCP / UDP)**.3
    - Oferece **ultra-alta performance** e baixa latência.
      
- **Gateway Load Balancer (GLB):**
    - Opera na **Camada 3 (IP)**.
    - Usado para implantar e dimensionar appliances de rede de terceiros.

- **Classic Load Balancer (CLB):**
    - **Descontinuado em 2023** (não recomendado para novos usos).
    - Operava nas Camadas 4 e 7.

 Para a aplicação, o Load Balancer teve como configuração a opção 'Voltado para a Internet' com IPv4 atrelado à VPC do trabalho. Em seguida, selecionado e inserido nas zonas em que as subredes públicas estão e sendo associado a elas sendo selecionado o grupo de segurança do serviço load balancer com o Listeners e roteamento tendo um grupo selecionado para a verificação de solicitação de conexão.

 ## Grupo de segurança Load Balancer
 No grupo de segurança criado para o serviço do Load Balancer, foram colocadas as regras de entrada:
 1. HTTP na porta 80 com IP qualquer/IP do host
 2. HTTPS na porta 443 com IP qualquer/IP do host

Enquanto a regra de saída é permitida todo o acesso IPv4 com o IP qualquer/IP do host.

## Listeners e roteamento
Para que ocorra a verificação de solicitação de conexão, o grupo de destino dos Listeners e roteamento é criado com a porta configurada. Nesse caso, foi selecionado o HTTP na porta 80 com protocolo HTTP1 na configuração básica de instância


# Funcionalidade - Auto Scaling Group
O Auto Scaling permite que aplicações se ajustem à demanda operando com capacidade ideal para economia de custos. Ele adiciona ou remove instâncias automaticamente conforme o aumento ou redução da carga, mantendo sempre o número mínimo e máximo de instâncias em funcionamento. Há também a realização da substituição automática de instâncias não saudáveis. Quando integrado ao Load Balancer, o sistema ganha maior eficiência:

- O Load Balancer recebe o tráfego da aplicação e distribui pelas instâncias ativas

- O Auto Scaling gerencia dinamicamente o número de instâncias:

- Adiciona novas instâncias quando criadas

- Remove instâncias encerradas

O Load Balancer monitora a saúde das instâncias e notifica o Auto Scaling para acionar substituições em caso de falha. Essa parceria garante adaptação contínua ao tráfego:
- Durante picos de demanda, o Auto Scaling lança novas instâncias que são imediatamente incorporadas na distribuição de carga pelo Load Balancer, prevenindo sobrecargas
- Em períodos de baixa demanda, o Auto Scaling remove instâncias excedentes para otimização de custos

