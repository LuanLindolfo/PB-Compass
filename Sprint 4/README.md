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

## Arquivos dos repositórios
**Arquivos da Aplicação (hello-app)**
  - *main.py*: Código-fonte da aplicação contendo a lógica da aplicação web, ou seja, a mensagem "Hello World" (que pode ser customizada em seguida) é exibida quando há acessa.

  - *Dockerfile*: Arquivo padrão de mapeamento de criação da imagem Docker da aplicação. Nele há instrução ao Docker sobre como empacotar o código, as dependências e o ambiente necessário para a aplicação rodar.

  - *requirements.txt*: É a lista das dependências do projeto. O Dockerfile usa este arquivo para saber quais bibliotecas (fastapi, uvicorn) precisam ser instaladas para que a aplicação funcione.

  - *main.yml*: Este é o workflow do GitHub Actions que une todas as peças da aplicação, automatiza o processo de contrução, a imagem Docker, publica e atualiza o repositório de manifestos.

**Arquivos de Manifesto (hello-manifests)**
  - *deployment.yaml*: Comunicador que diz ao Kubernetes como implantar a sua aplicação, definindo o número de réplicas (cópias) da aplicação que devem ser executadas e qual imagem Docker usar.

  - *service.yaml*: Este manifesto define como expor a sua aplicação. Ele cria um serviço que roteia o tráfego externo para os pods da sua aplicação, tornando-a acessível.


## Sincronização
