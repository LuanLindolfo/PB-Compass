# PB Compass - Sprint 1 
## Sumário
- [Objetivos](#objetivos)
- [Testes](#testes)
- [Configuração do Ambiente](#configuração-do-ambiente)
- [Configuração do Servidor Web](#configuração-do-servidor-web)
- [Instalação do serviço](#instalação-do-serviço)
- [Script de Monitoramento e Webhook via Discord](#script-de-monitoramento-e-webhook-via-discord)
- [Script via Bash](#script-via-bash)
- [Conclusão e melhorias futuras aplicáveis](#conclusão-e-melhorias-futuras-aplicáveis)


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
Com dois octetos fixos (10.0, definidos por /16), é possível obter 65.536 endereços. A partir desse entendimento, é possível compreender a dinâmica dos IPs de sub-redes, que se definem por setor a partir do endereço geral da VPC. O endereçamento da VPC é:

  #### Sub-redes, Tabela de Rotas, Gateway da Internet e Grupo de Segurança
As sub-redes (duas públicas e duas privadas) têm como principal objetivo definir as faixas de serviço que vão atuar, organizadas em Zonas de Disponibilidade a partir da Região de atuação. O endereçamento conta com três octetos fixos, definidos por /24, a partir do IP geral da VPC, possibilitando 256 endereços, dos quais geralmente 5 são reservados pela AWS.
Dessa forma, foram definidas a tabela de rotas principal (local) e a tabela de rotas com Gateway da Internet para que as instâncias determinem rotas de navegação para dentro e fora da VPC, a tabela de rotas das subredes públicas foi alocado com o endereço local da VPC (10.0.0.0/16) em conjunto com o internet gateway que garante o acesso à internet, enquanto a tabela de rotas das subrede privadas possui apenas o endereço local da VPC (10.0.0.0/16). Isso permite que as máquinas se encontrem na rede e mantenham um IP consistente, e que as aplicações internas acessem recursos externos. Dessa forma, a aplicação teve as subredes endereçadas em:

  1. Públicas: 10.0.1.0/24, 10.0.2.0/24
  2. Privadas: 10.0.3.0/24, 10.0.4.0/24

Posteriormente, o grupo de segurança foi definido com regras de entrada e saída para cada tipo de sub-rede (pública e privada), dadas as características de atuação de cada uma. Para as sub-redes públicas, os acessos de rede foram configurados, incluindo a configuração direta de SSH utilizando o IP da máquina local para acesso à máquina virtual na AWS.
Nessa mesma análise, foram consideradas as configurações de conexão local via SSH e as conexões com as sub-redes públicas.

  O Security Group da EC2 é estruturado da seguinte forma:
    #### **Inbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| HTTPS  | 443  | 0.0.0.0/0  |
| HTTP  | 80  | 0.0.0.0/0  |
| SSH  | 22  | IP da máquina  |

   Para fins de teste e averiguação se há algum problema no security group, o SSH pode ser permitido com origem 0.0.0.0/0. Não é recomendado pois a aplicação fica vulnerável.


#### **Outbound**
| Tipo  | Intervalo de Portas | Origem |
| ------------- | ------------- | ------------- |
| Todo o tráfego  | Tudo  | 0.0.0.0/0  |

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

  # Script de Monitoramento e Webhook via Discord
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
  chmod +x /usr/local/bin/monitoramentobin.sh
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
Dessa forma, este comando agenda a execução do script ```/usr/local/bin/monitoramentobin.sh``` com informação do diretório .bin no tempo determinado redirecionando a saída para o arquivo log ```cron_output.log```

**Teste do serviço**
Para testagem do funcionamento do serviço, basta parar o nginx por meio do comando
 ```bash
  systemctl stop nginx
  ```
  E em seguida, verificar no canal de texto no servidor do discord a mensagem de site offline

  Para reativar o site e realizar o teste para a mensagem de site online, basta começar o serviço do nginx por meio do comando
   ```bash
  systemctl start nginx
  ```
# Atualização (08/25) - Implementação de User Data e criação de Modelo De Execução 🤖
A arquitetura construída teve um upgrade automatizado, sendo eles:
  1. Automação Completa via User Data: Todo o processo de deploy e monitoramento do Nginx agora é 100% automatizado diretamente no User Data da EC2.

   - Essa é a grande novidade do projeto. A arquitetura agora é totalmente automatizada usando o User Data da AWS EC2. Isso significa que, a partir de agora, toda a configuração da instância é   feita de forma automática. O script no User Data é responsável por instalar o Nginx, baixar o código-fonte da aplicação HTML diretamente do repositório, e configurar o ambiente para estar no ar em poucos minutos.
    - Para averiguação do processo, basta consultar os logs:
      ```bash
      sudo cat /var/log/startup.log
      ```
      e a criação do diretório por meio dos comandos:
      ```bash
      df -h
      ```

  2.Implantação e Segurança Aprimoradas: O User Data agora cuida da instalação e configuração do Nginx, baixa o código-fonte HTML, e configura o UFW para garantir o acesso seguro por SSH.
  
  - A implantação do servidor Nginx foi aprimorada e também automatiza a segurança. O User Data não só instala o Nginx e as dependências necessárias, mas também configura o Firewall. Essa etapa crucial garante que apenas o tráfego necessário, como o acesso via SSH e as portas da aplicação, seja permitido, protegendo o servidor contra acessos indesejados, fator já aplicado manualmente anteriormente, mas agora de forma ágil e automatizada.
    
  3. Monitoramento e Resiliência Automáticos: Um script verifica o status do serviço Nginx a cada minuto. Em caso de falha, ele é reiniciado e uma notificação é enviada automaticamente para o Discord       via Webhook, garantindo a resiliência do projeto.

   - Um script de monitoramento foi implementado para verificar o status do serviço Nginx a cada minuto. Caso o serviço falhe, o script o reinicia automaticamente ter uma disponibilidade mais funcional. Além disso, para informação, uma notificação é enviada imediatamente para um canal do Discord por meio de um Webhook, averiguando o estado do site desde a disponibilidade, à indisponibilidade e ao reinício do serviço, tornando o monitoramento proativo sem a necessidade de intervenção manual.
  
  4. Criação de um Modelo de Execução construído com as Tags e a aplicação do User Data.
   - Para tornar o processo mais eficiente, foi criado um Modelo de Execução na AWS. Esse modelo já vem configurado com as tags necessárias e o script do User Data, simplificando a criação de novas instâncias. Dessa forma, o deploy e a aplicação são ágeis.

# **Conclusão e melhorias futuras aplicáveis**
Com base em todo o documento explicativo, é evidente a possibilidade de utilização de serviço webhook integrado ao linux agindo como ferramenta essencial de monitoramento de site, podendo ser integrado também ao Telegram. Em uma análise profunda, é possível mirar em melhorias em base de processamento ao ser inicializado e tendo a arquitetura construída utilizando o CloudFormation ou o Terraform, transformando-se um trabalho com maior alcance em automatização.

