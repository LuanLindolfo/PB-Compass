# CI/CD com o Github Actions
## Sumário
- [Objetivo](/Objetivo)

## Tecnologias Utilizadas 💻
![YAML](https://img.shields.io/badge/YAML-CB171E?style=for-the-badge&logo=yaml&logoColor=white)
![Rancher](https://img.shields.io/badge/Rancher-0075A8?style=for-the-badge&logo=rancher&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7420?style=for-the-badge&logo=argo&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)

## Objetivo 🎯
Automatizar o ciclo completo de desenvolvimento, build, deploy e execução de uma aplicação FastAPI simples, usando GitHub Actions para CI/CD, Docker Hub como registry, e ArgoCD para entrega contínua em Kubernetes local com Rancher Desktop

## Ferramentas e informações essenciais
Nesse projeto, foi realizado em um ambiente [WSL Ubuntu](https://apps.microsoft.com/detail/9NZ3KLHXDJP5?hl=neutral&gl=BR&ocid=pdpshare) com instalação do [Kubectl](https://kubernetes-io.translate.goog/docs/reference/kubectl/?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc) com [instalação no Ubuntu](https://kubernetes.io/pt-br/docs/tasks/tools/install-kubectl-linux), posteriormente foi criado o cluster que está sendo monitorado e gerenciado pelo [Rancher Desktop](https://rancherdesktop.io) que além do monitoramento do cluester e dos nós, também roda e inicia o [Docker Engine no Ubuntu](https://docs.docker.com/engine/install/ubuntu). outro fator crucial é a instalação do [Python 3](https://python.org.br/instalacao-windows) que foi instalado no sistema operacional nativo da máquina (Windows) e gerenciado via [Visual Studio Code](https://code.visualstudio.com), bem como foi instalado o [Git](https://git-scm.com/downloads) para gerenciamento local dos repositórios via Visual Studio Code por meio da clonagem dos repositórios com o comando:

```bash
git clone <URL_DO_REPOSITÓRIO>
```

Após a clonagem, foi feito o login na conta do GitHUb para acesso ao repositório. Dessa forma, é crucial tais ferramentas para a criação e o manejo da aplicação, sendo adequada a cada sistema Operacional, cada tipo de máquina e cada necessidade.

### O que é GitHub Actions?
GitHub Actions é uma plataforma de integração contínua e entrega contínua (CI/CD) que permite automatizar a compilação, testar e pipeline de implantação.

# Arquitetura do Projeto
A solução consiste em dois repositórios e duas ferramentas principais de automação:
 - *hello-app:* Repositório que contém o código da aplicação FastAPI, o Dockerfile e o workflow de CI/CD do GitHub Actions (ci-cd.yml).
 - *hello-manifests:* Repositório que contém os manifestos de implantação em Kubernetes (deployment.yaml e service.yaml).

   O GitHub Actions é responsável pela Integração Contínua (CI), onde ele automatiza a construção da imagem Docker e a envia para o Docker Hub. O ArgoCD é responsável pela Entrega Contínua (CD), que monitora o repositório de manifestos e sincroniza as alterações no cluster Kubernetes.

  ## Manifestos
  Manifestos são a base da aplicação em metadados, ou seja, arquivos contendo as configurações desejadas para a aplicação e infraestrutura.

  ## Pipelines
  Pipeline é conceituado em etapas de processamento de processos conectados executando tarefas em um ordem e preparando dados, posteriormente encaminhando para um repositório.

  ### Funcionamento das etapas de processos
  - O GithubActions detecta um novo commit no repositório *hello-app*, acionando assim o pipeline do CI/CD
  - Uma imagem é criada no Docker e publicada no DockerHub
  - Em seguida, o arquivo deployment.yaml é atualizado no repositório *hello-manifests*
  - Toda a atualização é monitorada pelo ArgoCD, basta sincronizar na aplicação criada no ArgoCD

## Repositório
Para a atual aplicação, foram criados dois repositórios chamados [*hello-app*](https://github.com/LuanLindolfo/hello-app) e *hello-manifests*
- No repositório [*hello-app*](https://github.com/LuanLindolfo/hello-app) foram implementadas algumas variáveis de segredo (Configurações no nível do repositório > Secrets and Variables > Actions > New Repository secret) para que não fiquem expostos os valores, sendo utilizadas nos códigos yaml que constroem a aplicação. Dessa forma, estão resguardada com relação a perigos externos, sendo elas:
  1. DOCKER_USERNAME: É a identidade para autenticar no Docker Hub durante o processo de build e publicação da imagem.
  2. DOCKER_PASSWORD:  É a senha da conta do Docker Hub. Ela é usada em conjunto com o DOCKER_USERNAME para autenticar a sua conta. Foi utilizada para questão opcional caso a variável DOCKER_TOKEN não funcione. Deve ser implementada no código.
  4. DOCKER_TOKEN: Um Personal Access Token (PAT). É uma alternativa mais segura para a senha, e é gerado diretamente na conta do DockerHub. Possui a vantagem de ser revogado a qualquer momento.
  5. SSH_PRIVATE_KEY: A chave privada SSH que foi gerada. Ela é a credencial para autenticar e obter acesso de escrita no repositório de manifestos (hello-manifests), permitindo que o GitHub Actions faça commits e pushs das alterações.
  6. SSH_PRIVATE_KEY.pub: Extensão púiblica da chave SSH com permissão para escrita no repositório de manifestos (hello-manifests).
  7. MANIFESTS_REPO_URL: O URL do seu repositório de manifestos, que o GitHub Actions usa para fazer a clonagem.
  8. MANIFESTS_TOKEN: É um nome que pode ser dado a um token de acesso pessoal do GitHub para autenticar o push para o repositório de manifestos, sendo uma opção válida quanto à chave SSH

Dentro do repositório foi criado o [Workspace](/.github/workflows) para compilar o código e realizar as actions, dentro da construção do código, há blocos de tarefas:

1. Checkout do Código da Aplicação: A primeira etapa baixa o código-fonte da sua aplicação do repositório e garante que o pipeline tenha acesso aos arquivos para construir a imagem Docker. É importante analisar que o código pode ficar configurado para disparar quando selecionado o run do worflow ou em cada pull request do branch main (principal), na seguinte estrutura:
```yaml
     on:
  workflow_dispatch:
  pull_request:
    branches:
      - main
```
   Porém pode ser alterado para que rode a cada push no repositório ou em cada pull request do branch main (principal) na seguinte maneira:
```yaml
     on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
```
2. Login no Docker Hub: Realiza o login no Docker Hub, de forma segura, usando credenciais seguras armazenadas como variáveis de ambiente (secrets). O login é necessário para que a máquina virtual tenha permissão para publicar (fazer push) a imagem Docker.

4. Construir e Publicar a Imagem Docker: O workflow constrói a imagem Docker da aplicação. Após a construção, ele publica a imagem no Docker Hub com duas tags:

 - latest: Uma tag que aponta para a versão mais recente.

 - github.sha: Uma tag única com o ID (hash) do commit que acionou o workflow. Isso é útil para rastrear qual versão do código corresponde a qual imagem.

4. Checkout do Repositório de Manifestos: Esta é uma etapa crucial para a entrega contínua. O workflow acessa um segundo repositório, *hello-manifests*, que contém arquivos de configuração (manifestos) para implantação, como um deployment.yaml para Kubernetes. A permissão de acesso é concedida por um token específico e seguro (MANIFESTS_TOKEN).

5. Atualizar a Tag da Imagem no Arquivo de Manifestos: O script sed nesta etapa procura e substitui a tag da imagem no arquivo deployment.yaml dentro da pasta manifests. Ele atualiza o nome da imagem para a tag única gerada no Passo 3 (github.sha), garantindo que a configuração de implantação sempre aponte para a versão correta e mais recente da sua imagem.

6. Commit e Push das Alterações: Por fim, o pipeline faz o commit e o push da alteração que foi feita no Passo 5. Ele adiciona o arquivo deployment.yaml modificado, cria uma mensagem de commit e envia a alteração para o repositório de manifestos. Isso faz com que o repositório de implantação esteja sempre sincronizado com a última versão do seu código.

O processo poderá ser prosseguido desde que o deploy e a build do worflow seja bem sucedio, pode ser visto na aba "actions" no repositório.

## Arquivos dos repositórios
**Arquivos da Aplicação (hello-app)**
  - *main.py*: Código-fonte da aplicação contendo a lógica da aplicação web, ou seja, a mensagem "Hello World" (que pode ser customizada em seguida) é exibida quando há acessa.

  - *Dockerfile*: Arquivo padrão de mapeamento de criação da imagem Docker da aplicação. Nele há instrução ao Docker sobre como empacotar o código, as dependências e o ambiente necessário para a aplicação rodar.

  - *requirements.txt*: É a lista das dependências do projeto. O Dockerfile usa este arquivo para saber quais bibliotecas (fastapi, uvicorn) precisam ser instaladas para que a aplicação funcione.

  - *main.yml*: Este é o workflow do GitHub Actions que une todas as peças da aplicação, automatiza o processo de contrução, a imagem Docker, publica e atualiza o repositório de manifestos.

**Arquivos de Manifesto (hello-manifests)**
  - *deployment.yaml*: Comunicador que diz ao Kubernetes como implantar a sua aplicação, definindo o número de réplicas (cópias) da aplicação que devem ser executadas e qual imagem Docker usar.

  - *service.yaml*: Este manifesto define como expor a sua aplicação. Ele cria um serviço que roteia o tráfego externo para os pods da sua aplicação, tornando-a acessível.

## O que é GitOps?
GitOps utiliza a base DevOps (containerização, gerenciamento e ambientação em nuvem) utilizando o Git como única fonte para estruturação e aplicação.

## O que é ArgoCD?
O ArgoCD é uma ferramenta declarativa e open-source para Entrega Contínua (CD) no Kubernetes. Ela faz parte das ferramentas GitOps.

O principal objetivo do ArgoCD é garantir que o estado do seu cluster Kubernetes esteja sempre sincronizado com os arquivos de configuração que estão no repositório Git.

**Seus propósitos principais são:**

- Sincronização Automática: Detecta mudanças nos manifestos do Git e as aplica no cluster.

- Gerenciamento de Ambientes: Ajuda a gerenciar múltiplos ambientes (como produção e desenvolvimento) de forma consistente.

- Visibilidade e Monitoramento: Oferece uma interface web para visualizar o estado das aplicações e identificar problemas rapidamente.

**Benefícios**
- Aumenta a visibilidade do que está rodando no cluster.

- Reduz o downtime (tempo de inatividade) com implantações mais rápidas e automáticas.

- Melhora a segurança ao eliminar a necessidade de acesso SSH direto ao cluster.

- Facilita a colaboração em equipes de DevOps.

## Namespace do ArgoCD
Para a ambientalização da aplicação, foi criado um namespace para o ArgoCD com o comando
```bash
kubectl create namespace argocd
```
Um namespace é conceituado em uma espécie de pasta que organiza e isola as funções e os agentes no cluster sem que tenham conflito e utilizem o mesmo recurso.

Em seguida foi acessada à aplicação do ArgoCD com um comando que faz a porta da aplicação ser ouvida e consequentemente abrir a porta de acesso, por meio do comando:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Os comandos funcionam da seguinte forma:
- kubectl port-forward: Este é o comando que estabelece a conexão e encaminha o tráfego de uma porta local para uma porta de um serviço dentro do cluster.

- svc/argocd-server: O comando identifica o serviço do Kubernetes (servidor do ArgoCD) ao qual você quer se conectar.

- -n argocd: Esta flag especifica o namespace onde o serviço está localizado, instalando em seu próprio namespace para organização.

- 8080:443: O tráfego que chega na porta 8080 na máquina local será roteado para a porta 443 do serviço do ArgoCD, porta padrão do tráfego seguro HTTPS.

Em seguida basta acessar a aplicação por meio da pesquisa: https://localhost:8080
É importante ressaltar que a porta do tráfego, desde que na faixa de permissão, pode ser alterada para: 9000, 9001 e afins

## Aplicação ArgoCD
No ArgoCD foi criada a aplicação para monitoramento, as informações foram preenchidas com as seguintes bases:
- Application Name: Nome Customizado pra aplicação
- Project Name: default
- SYNC POLICY: Manual
- Repository URL: Link do repositório de manifestos (dado qe será monitorado quaisquer atualização no doc de configuração/deploy)
- Revision: HEAD
- Path: . (Para acesso na pasta raíz)
- Cluster URL: in-cluster (cluster local)
- Namespace: default

Em seguida, basta verificar no block da aplicação que estará "OutOfSync", basta sincronizar clicando no botão "SYNC" e aguardar a sincronização e a aplicação ficar Synced e HEALTHY que estará sincronizada e estável.

Posteriormente, basta acessar a aplicação por meio do comando de acesso e escuta à porta utilizando a porta 9001 como exemplo: 
```bash
kubectl port-forward svc/hello-app-service 9001:8080
```
- kubectl port-forward: Inicia o processo de redirecionamento de portas.

- svc/hello-app-service: Identifica o serviço do Kubernetes de acesso, neste caso, o serviço que expõe a aplicação (hello-app-service).

- 9001:8080: Roteia o tráfego. Tudo o que acessar na porta 9001 na sua máquina local (localhost:9001) será enviado para a porta 8080 do serviço dentro do Kubernetes.

