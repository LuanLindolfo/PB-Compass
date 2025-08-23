# Aplicação do Wordpress em alta-disponibilidade
## Sumário
- [User Data](/Sprint%202/user%20data)
- [Objetivos](#objetivos)
- [Componentes](#componentes)
- [Funcionalidade](#funcionalidade)
- [Security Group](#security-group)
- [Acesso à aplicação do WordPress](#acesso-à-aplicação-do-wordpress)
- [Possíveis Melhorias](#possíveis-melhorias)

## Tecnologias Utilizadas 💻
![WordPress](https://img.shields.io/badge/WordPress-21759B?style=for-the-badge&logo=wordpress&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![Docker Hub](https://img.shields.io/badge/Docker_Hub-000000?style=for-the-badge&logo=docker&logoColor=white)
![Docker Compose](https://img.shields.io/badge/Docker_Compose-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=for-the-badge&logo=yaml&logoColor=white)
![Rancher](https://img.shields.io/badge/Rancher-0075A8?style=for-the-badge&logo=rancher&logoColor=white)
![Windows](https://img.shields.io/badge/Windows-0078D4?style=for-the-badge&logo=windows&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-121011?style=for-the-badge&logo=gnu-bash&logoColor=white)
![User Data](https://img.shields.io/badge/User_Data-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Amazon EC2](https://img.shields.io/badge/Amazon_EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Amazon RDS](https://img.shields.io/badge/Amazon_RDS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Amazon EFS](https://img.shields.io/badge/Amazon_EFS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Load Balancing](https://img.shields.io/badge/Load_Balancing-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Amazon Auto Scaling](https://img.shields.io/badge/Amazon_Auto_Scaling-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)


# Objetivos 🔍
## O presente projeto trata-se de uma aplicação prática de conhecimentos adquiridos na trilha DevSecOps do programa de bolsas da Compass. Dessa forma, a documentação se faz crucial para a aplicação dos conhecimentos adquiridos e do projeto proposto

# Componentes 🛠️
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

# Funcionalidade ⚙️
## Funcionalidade VPC
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

## Funcionalidade - RDS
O RDS é um serviço gerenciado de banco de dados relacional que suporta SQL (PostgreSQL, MySQL, MariaDB, Oracle, SQL Server, DB2). Este serviço permite:

1. Organização estruturada de dados: Facilita a compreensão e o gerenciamento das informações.

2. Pesquisa ágil: Utiliza índices para consultas rápidas e eficientes.

3. Relacionamento e integridade: Garante consistência e integridade referencial dos dados através de relações definidas.

4. Otimização: Adequa os recursos e configurações do banco de dados às necessidades específicas da aplicação.

Na aplicação, foi criado um banco de dados MySQL na versão mais recente (8.4.6) no modelo de nível gratuito e consequentemnet a aplicação Single-AZ. O user e a senha foram criados com gerenciamento de credenciais autogerenciado com os recursos dr.t3.micro e armazenamento padrão.
A conectividade do RDS foi selecionado para se conectar à EC2, logo, o RDS irá se conectar aos recursos da EC2 ativa tendo contato direto como desejado na aplicação. O tipo de rede em ipv4 e o grupo de segurança selecionado ao que foi ajustado para a aplicação do banco de dados e com a seleção da zona de disponibilidado de RDS.

O RDS foi aplicado em cada zona de disponibilidade onde se conecta à todas as subredes privadas.

### Grupo de segurança RDS
O grupo de segurança do RDS foi criado para a finalidade de conexão com a EC2 somente ouvindo-a. Logo, o grupo de segurança do RDS tem como regras de entrada:
1. MYSQL na porta 3306 com IP qualquer/IP do host

Na regra de saída, é permitido todo o acesso IPv4 com o IP qualquer/IP do host.

## Funcionalidade - EFS
O EFS é um serviço de armazenamento em rede totalmente gerenciado e Multi-AZ (implantado em múltiplas zonas de disponibilidade dentro da mesma região). Ele utiliza o protocolo NFSv4, montado no diretório /opt/data das instâncias EC2. Na aplicação atual, ele teve a montagem em outro diretório conforme atestado via user data.

Sua arquitetura garante alta disponibilidade:

1. Funcionalidade distribuída: Opera em várias zonas de disponibilidade simultaneamente.

2. Tolerância a falhas: Se uma instância EC2 falhar, as demais mantêm acesso ininterrupto aos arquivos no EFS.

3. Independência de instâncias: O sistema de arquivos não depende do estado de instâncias individuais para funcionar — permanece disponível mesmo que todas as instâncias associadas sejam desativadas.

Na aplicação do Wordpress, o EFS foi aplicado nas 4 subredes privadas e alocado apenas no grupo de segurança do EFS-NFS em cada subrede.

Nesta aplicação, o EFS é inserido nas subredes privadas com o Security Group montado para a atuação do EFS, para acompanhar a construção correta do ponto de montagem do EFS (/mnt/efs) basta conferir o log do user data pelo comando:
```
sudo cat /var/log/startup.log
```
E para conferir se o diretório foi criado corretamente basta verificar com o comando abaixo por meio do DNS Name ou ID do EFS em conjunto com o diretório
```
df -h
```
Em caso de não funcionamento, poderá ser testado o EFS padrão sem personalização.

### NFS
O NFS é o protocolo utilizado pelo EFS. Nesse modelo, o NFS define um serviço como servidor, nesse caso, o EFS atua como servidor (fonte central dos arquivos), enquanto as instâncias EC2 funcionam como clientes que acessam esses arquivos. Essa arquitetura permite múltiplos acessos simultâneos ao mesmo arquivo, otimizando o acesso em tempo real inclusive durante atualizações. Quando uma instância EC2 monta o EFS via protocolo NFS, ela envia comandos para acessar os arquivos do servidor EFS, disponibilizando-os localmente no sistema, no diretório /mnt/efs.

## Funcionalidade - Load Balancer
O Load Balancer é um serviço que forne o balanceamento de carga entre as aplicações com vantagens aplicadas como o Gerenciamento da AWS (onde a AWS trata a manutenção, upgrades, funcionalidade e a alta disponibilidade), facilidade de Configuração por meio de poucas opções de configuração para simplificar o uso, e custo benefício reduzindo significativamente o esforço de manutenção e integração por parte do usuário. O Load Balancer trata um tópico importante sendo ele: Escabilidade Vertical (aumentando o poder computacional de uma instância) e Horizontal (aumentar o número de instâncias ou sistemas para sua aplicação.)

Há quatro tipos de Load Balancers:
- Application Load Balancer (ALB):
    - Opera na Camada 7 (HTTP / HTTPS).
    - Ideal para aplicações web e microsserviços.
      
 - Network Load Balancer (NLB):
    - Opera na Camada 4 (TCP / UDP).3
    - Oferece ultra-alta performance e baixa latência.
      
- Gateway Load Balancer (GLB):
    - Opera na Camada 3 (IP).
    - Usado para implantar e dimensionar appliances de rede de terceiros.

- Classic Load Balancer (CLB):
    - Descontinuado em 2023 (não recomendado para novos usos).
    - Operava nas Camadas 4 e 7.

 Para a aplicação, o Load Balancer teve como configuração a opção 'Voltado para a Internet' com IPv4 atrelado à VPC do trabalho. Em seguida, selecionado e inserido nas zonas em que as subredes públicas estão e sendo associado a elas sendo selecionado o grupo de segurança do serviço load balancer com o Listeners e roteamento tendo um grupo selecionado para a verificação de solicitação de conexão.

### Listeners e roteamento
Para que ocorra a verificação de solicitação de conexão, o grupo de destino dos Listeners e roteamento é criado com a porta configurada. Nesse caso, foi selecionado o HTTP na porta 80 com protocolo HTTP1 na configuração básica de instância

É válido lembrar que o Load Balancer é adaptado para compreender o sinal da apicação e compreender o bem estar dentro da margem do código, nessa caso, atua como 200 - 399.

## Funcionalidade - Auto Scaling Group
O Auto Scaling permite que aplicações se ajustem à demanda operando com capacidade ideal para economia de custos. Ele adiciona ou remove instâncias automaticamente conforme o aumento ou redução da carga, mantendo sempre o número mínimo e máximo de instâncias em funcionamento. Há também a realização da substituição automática de instâncias não saudáveis. Quando integrado ao Load Balancer, o sistema ganha maior eficiência:

- O Load Balancer recebe o tráfego da aplicação e distribui pelas instâncias ativas

- O Auto Scaling gerencia dinamicamente o número de instâncias:

- Adiciona novas instâncias quando criadas

- Remove instâncias encerradas

O Load Balancer monitora a saúde das instâncias e notifica o Auto Scaling para acionar substituições em caso de falha. Essa parceria garante adaptação contínua ao tráfego:
- Durante picos de demanda, o Auto Scaling lança novas instâncias que são imediatamente incorporadas na distribuição de carga pelo Load Balancer, prevenindo sobrecargas
- Em períodos de baixa demanda, o Auto Scaling remove instâncias excedentes para otimização de custos

Na aplicação, o modelo de execução (configurado para a criação da EC2) é selecionado. Esse modelo inclui o user data (com instalação do Docker, WordPress, Apache, PHP, MySQL e outros comandos), as tags e a configuração completa da EC2 para criação. Na aba de rede, a instância é associada às subredes privadas, conforme solicitado no projeto. Em seguida, é anexada a um balanceador de carga existente, selecionando-se o Load Balancer criado anteriormente. Para o grupo de destino, a opção de verificação de integridade do Elastic Load Balancing é ativada para monitoramento das instâncias.

A capacidade desejada varia conforme o uso. Neste caso, foi definida como 1, com escalabilidade de capacidade mínima e máxima em 1 e 2, respectivamente (para desativar o auto scaling, defina todas as capacidades como 0). A política de dimensionamento com monitoramento de métricas está ativada para ajuste proporcional da escala e da capacidade desejada.

O tráfego pode ter um aumento simulado para testar a capacidade do ASG
## Funcionalidade - Bastion Host
O bastion atua como ponto intermediário de segurança para uma aplicação segura, neste caso uma rede privada. Todas as conexões remotas devem passar por ele. A aplicação fica visível apenas para o Bastion Host, deixando assim o controle da aplicação menos vulnerável e mais centralizado, facilitando o monitoramento e aumentando a segurança contra ataques. É importante lembrar que o bastion pode ser configurado para uma autenticação de dois fatores.

### Bastian na aplicação do Wordpress (comandos)
Nesta aplicação, para que o bastian acesse a subrede privada, foi estabelecido o comando até a chave .pem que acessa a subrede privada e foi aplicados os comandos:
```
"eval (ssh-agent -c)"
```
    
```
ssh-add chave.pem
```
    
```
ssh -A -i "tuachave.pem" ubuntu@ip_publico_bastion
```
    
```
ssh ubuntu@ip_privado_subrede_privada
```
    
Observação: Há casos em que no user root pode dar falha

## Security Group 🛡️
O Security Group é um componente em escala de EC2 que serve como firewall para entrada e saída permitindo tráfego em nível de IP. Dentro do projeto, foi arquitetado para cada componente que faz parte da aplicação do Wordpress.

### Security Group - EC2
#### **Inbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| SSH  | 22  | ID do Security Group do Bastian  |
| MYSQL/Aurora  | 3306  | 0.0.0.0/0  |
| HTTP  | 80  | ID do Security Group do Load Balancer  |


#### **Outbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| Todo o tráfego  | Tudo  | 0.0.0.0/0  |
| NFS  | 2049  | 0.0.0.0/0  |

### Security Group - Load Balancer
#### **Inbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| HTTP  | 80  | 0.0.0.0/0  |
| HTTPS  | 443  | 0.0.0.0/0  |

#### **Outbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| Todo o tráfego  | Tudo  | 0.0.0.0/0  |

### Security Group - EFS
#### **Inbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| NFS  | 2049  | ID do Security Group da EC2  |

#### **Outbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| Todo o tráfego  | Tudo  | 0.0.0.0/0  |

### Security Group - Bastion
#### **Inbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| SSH  | 22  | IP local da máquina de atuação  |

#### **Outbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| Todo o tráfego  | Tudo  | 0.0.0.0/0  |

### Security Group - RDS
#### **Inbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| MYSQL/Aurora  | 3306  | ID do Security Group da EC2  |

#### **Outbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| Todo o tráfego  | Tudo  | 0.0.0.0/0  |


O security group fará as devidas comunicações, que se estruturam em:

EC2: O tráfego necessário incluirá a aplicação HTTP, onde ouvirá essa porta para o tráfego da aplicação do WordPress na rede. O SSH será diretamente do Bastion, que fará a conexão com a rede privada para que o User Data seja iniciado e seja possível conectar à internet (em conjunto com o NAT atrelado à rede privada). O MySQL/Aurora terá a permissão para conexão com o banco de dados e todos os registros aplicados. Na saída, haverá o tráfego de dados para o EFS por meio do protocolo utilizado (NFS).

EFS: O tráfego necessário terá a entrada NFS com o tráfego de dados que chega até ele, permitindo a ligação com as regiões sem o risco de perda total dos dados, com saída permitindo o tráfego dos dados.

RDS: O tráfego necessário será para ouvir apenas os dados do EC2, conforme solicitado no projeto.

Bastion: O tráfego necessário será para a conexão com a sub-rede privada, que terá a aplicação do Docker e WordPress via User Data, sendo possível se conectar pelo IP privado da sub-rede e pelo IP público do Bastion.

Em caso de algum erro no security group que não seja possível encontrar, é possível permitir todo o tráfego para averiguar as permissões errôneas.

# Acesso à aplicação do Wordpress 💻
A construção da aplicação é feita para ouvir a porta HTTP (80), desde o Security Group, ao Docker Compose e ao Grupo de Destino do Application Load Balancer, dessa forma, o acesso é feito a partir das análises:
1. Atualização das informações do EFS no user data
   - Lembrar sempre de atualizar o ID do EFS e o DNS Name do EFS no User Data no Modelo de Execução (atualizanedo o modelo e definindo-o como padrão) e garantindo que o Auto Scaling Group lance a versão atualizada da instância.
2. Acompanhamento da Saúde da Aplicação
   - Verificar se a aplicação que foi introduzida a partir do Auto Scaling Group consta como "Healthy" no Application Load Balancer, em caso de constar como "Unhealthy" há algum problema na aplicação e precisa ser investigado.
   - Aguardar calguns minutos desde o lançamento da EC2 até a atualização de saúde no Application Load Balancer pois o processo pode demorar um pouco para ser atualizado.
3. Acesso via HTTP
   - Quando saudável a aplicação, é possível acessar o Wordpress por meio da pesquisa com o acesso HTTP, sendo:
   ```
   http://(DNS name do Application Load Balancer)
   ```
   - Garanta que a aplicação esteja sendo pesquisada pelo HTTP, em caso contrário, não será possível ser acessada.

# Possíveis Melhorias ⚙️
1. A criação da estruturação da aplicação por ser feita por meio do Terraform ou AWS CloudFormation
   - Poder ser feita a codificação a partir da estrutura já criada
2. Monitoramento com CloudWatch
