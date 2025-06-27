# PB Compass - Sprint 1 
## Sumário
- [Objetivos](#objetivos)
- [Testes](#testes)
- [Configuração do Ambiente](#configuração-do-ambiente)
- [Uso](#uso)
- [Contribuição](#contribuição)
- [Licença](#licença)


# Objetivos

O presente projeto tem como objetivo a resolução do desafio:

__Etapa 1: Configuração do Ambiente__

__1 - Criar uma VPC com com 2 sub-redes públicas e 2 privadas.__\
  1.1 - Criar uma instância EC2 na AWS, com Sistema Operacional Ubuntu na VPC criada e na sub-rede pública.
  
__Etapa 2: Configuração do Servidor Web__

__2 - Subir um servidor Nginx na EC2.__\
  2.1 - Criar uma página simples em html que será exibida dentro do servidor Nginx.

__Etapa 3: Monitoramento__
  
__3 - Script de Monitoramento + Webhook.__\
  3.1 - Criação de um scipt que monitore a cada 1 minuto se o site está disponível (rodando normalmente), informando por meio do Discord a indisponibilidade e a disponibilidade do serviço.\
  3.2 - Armazenamento de log em um local do servidor.

  # Testes
  Para possibilitar a execução de testes e manutenção de comandos, foi utilizado o subsistema Ubuntu (na versão 24.04.1 LTS) no ambiente Windows. Essa solução permite uma integração dinâmica durante a implementação dos exercícios práticos.

  Disponível em: [Ubuntu 24.04.1 LTS](https://apps.microsoft.com/detail/9NZ3KLHXDJP5?hl=neutral&gl=BR&ocid=pdpshare)

  # Configuração do Ambiente
  Utilizando o ambiente da AWS, o ambiente foi preparado para utilizar um ambiente virtualizado em nuvem. Dessa forma, foi utilizada a VPC (Virtual Private Cloud) para que sejam executados os comandos e gerenciada a comunicação do projeto como um todo.
  ### Configuração da VPC
Dentre as configurações de estruturação da VPC, a análise ocorre dentro dos aspectos de sub-redes, tabelas de rotas, Gateway da Internet e grupos de segurança no Centro de Dados da Virgínia do Norte. Na estruturação da VPC, foi utilizado o endereçamento IP 10.0.0.0/16 devido à facilidade de leitura e à vasta possibilidade de endereçamento.
Com dois octetos fixos (10.0, definidos por /16), é possível obter 65.536 endereços. A partir desse entendimento, é possível compreender a dinâmica dos IPs de sub-redes, que se definem por setor a partir do endereço geral da VPC.

  #### Sub-redes, Tabela de Rotas, Gateway da Internet e Grupo de Segurança
As sub-redes (duas públicas e duas privadas) têm como principal objetivo definir as faixas de serviço que vão atuar, organizadas em Zonas de Disponibilidade a partir da Região de atuação. O endereçamento conta com três octetos fixos, definidos por /24, a partir do IP geral da VPC, possibilitando 256 endereços, dos quais geralmente 5 são reservados pela AWS.
Dessa forma, foram definidas a tabela de rotas principal (local) e a tabela de rotas com Gateway da Internet para que as instâncias determinem rotas de navegação para dentro e fora da VPC. Isso permite que as máquinas se encontrem na rede e mantenham um IP consistente, e que as aplicações internas acessem recursos externos.

Posteriormente, o grupo de segurança foi definido com regras de entrada e saída para cada tipo de sub-rede (pública e privada), dadas as características de atuação de cada uma. Para as sub-redes públicas, os acessos de rede foram configurados, incluindo a configuração direta de SSH utilizando o IP da máquina local para acesso à máquina virtual na AWS.
Nessa mesma análise, foram consideradas as configurações de conexão local via SSH e as conexões com as sub-redes públicas.

  ### Configuração da EC2
Com base nas tags de recursos, foi criada uma instância EC2 (Máquina virtualizada) com o sistema operacional Ubuntu (versão 24.04.1 LTS) e tipo de instância t3.micro. Esta escolha deve-se ao fato de a t3.micro oferecer suporte e refletir a evolução mais recente e atualizada da Amazon.
A instância EC2 foi configurada na VPC criada anteriormente, na sub-rede pública. Seu acesso é feito via SSH por meio de um par de chaves na extensão .pem, utilizando um grupo de segurança já configurado anteriormente na VPC, permitindo que as configurações de conexão local via SSH e as conexões com as sub-redes públicas fossem consideradas.

Após as execuções, é possível conectar-se à instância EC2 utilizando o IP da máquina local através do comando SSH (no subsistema Ubuntu) ou diretamente pela conexão fornecida pela AWS.

# Configuração do Servidor Web
