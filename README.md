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
Foi utilizado o servidor web NGINX que tem forte característica estável e com recursos eficiente. 

## Instalação do serviço

Para subir o servidor Nginx, foram realizados os seguintes passos com o usuário root, e se não for possível usuário root, utilizando o comando `sudo`:

* **Atualização dos pacotes:**
    ```bash
    sudo apt update
    ```
* **Instalação o Nginx:**
    ```bash
    sudo apt install nginx
    ```
* **Permitindo o serviço no firewall (no Ubuntu é ufw) e ative o serviço:**
    ```bash
    sudo ufw enable
    ```
* **Listando os serviços pelo ufw:**
    ```bash
    sudo ufw app list
    ```
* **Permitindo o tráfego HTTP e HTTPS:**
    ```bash
    sudo ufw allow 'Nginx HTTP'
    sudo ufw allow 'Nginx HTTPS'
    ```
* **Verificando o status do firewall para ver se está ativo e permitido:**
    ```bash
    sudo ufw status
    ```
* **Verificando o status do Nginx:**
    ```bash
    systemctl status nginx
    ```
* **Teste no navegador para ver se está funcionando:**
    Acesse o IP da sua máquina EC2 no navegador. Você deverá ver a página de boas-vindas do Nginx.
   Pesquise o IP e:
  
    ```bash
    http://{ip} ou https://{ip}
    ```
    Em caso de teste no subsistema, é possível acessar com o endereço da máquina atuante (desde que seja permitido no grupo de segurança)
    Em outro caso, há a possibilidade se conectar, por meio da máquina, ao servidor web do Nginx
    ```bash
  http://localhost
    ```

    ### Configuração do HTML do site
  Para melhor visualização, o hmtl do site, para acesso com IP, pode ser alterado por meio do editor VI no diretório 
      ```
  /usr/share/nginx/html/
      ``` ou pelo diretório do local host
        ```
/var/www/html/
      ```

  # Script de Monitoramento + Webhook via Discord
  ## Webhook no canal de texto do discord
  Para recebimento da notificação de monitoramento do site, foi criado um servidor no discord com um canal de texto para o alerta. Dentro destas configurações, no canal de texto foi criado um webhook em integrações do canal e gerado um link que será utilizado no scirpt.


## Script via Bash
Para que o script seja corretamente implementado, é necessário a criação e implementação de logs (arquivos que armazenam informações de um script como variáveis, comandos e outras informações). Nesse caso, os logs são armazenados no diretório ``` /var/log/ ```, sendo assim, o mesmo local onde será colocado o log do script de monitoramento.
O arquivo bin também é de extrema importância por ser o local de armazenamento de executáveis, que nesse caso, será o script instalado e não gerenciado pelo sistema.
* **Navengando até o diretório do ```.log```**
  ```bash
   cd /var/log/
  ```
* **Criando o log**
  ``` bash
  mkdir -p monitoramento_web
  ```
  
  Nesse caso, o comando ``` mkdir ``` cria o arquivo, o ``` -p ``` ignora erros de forma inteligente e, em seguida, o nome é implementado.
* **Navegando até o diretório do ```.bin```**
  Com base nas características do processo, o caminho será o diretório bin onde script implementados manualmente são alocados.
  ```bash
  /usr/local/bin/
  ```

  * **Criando e alterando o conteúdo do arquivo para funcionamento do scirpt**
  ```bash
  vi /usr/local/bin/monitoramentobin.sh
  ```
Após esse comando, basta implementar o script do melhor jeito com mensagens personalizadas. O script implementado está anexado ao repositório.

* **Script executável**
    ```bash
  chmod +x /usr/local/bin/monitorar_site.sh
     ```
    Dessa forma, ao utilizar o  ```chmod ``` (Change Mode) as permissões do arquivo são alteradas em conjunto com o comando  ```+x ``` para que a permissão de execução seja aplicada, caso contrário, o script não irá funcionar como um programa executável.
* **Utilizando o crontab para tempo de monitoramento**
* **Abrindo para edição**
* ```bash
  crontab -e
  ```
  Após este processo, o utilitário crontab (para agendamento de processos em horários específicos ou em intervalos) será aberto, vem instalado por vezes, mas caso contrário, será instalado.
 * **Adicionando a linha de agendamento ao final do arquivo log**
  ```bash
* * * * * /usr/local/bin/monitoramentobin.sh >> /var/log/monitoramento_web/cron_output.log 2>&1
```
Dessa forma, este comando agenda a execução do script ```/usr/local/bin/monitoramentobin.sh``` com informação do diretório .bin no tempo determinado redireciionando a saída para o arquivo log ```cron_output.log```
