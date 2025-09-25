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
- No repositório [*hello-app*](https://github.com/LuanLindolfo/hello-app) foram implementadas algumas variáveis de segredo para que não fiquem expostos os valores, sendo utilizadas nos códigos yaml que controem a aplicação. Dessa forma, estão resguardada com relação a perigos externos, sendo elas:
  1. DOCKER_USERNAME: É a identidade para autenticar no Docker Hub durante o processo de build e publicação da imagem.
  2. DOCKER_PASSWORD:  É a senha da conta do Docker Hub. Ela é usada em conjunto com o DOCKER_USERNAME para autenticar a sua conta. Foi utilizada para questão opcional caso a variável DOCKER_TOKEN não funcione. Deve ser implementada no código.
  4. DOCKER_TOKEN: Um Personal Access Token (PAT). É uma alternativa mais segura para a senha, e é gerado diretamente na conta do DockerHub. Possui a vantagem de ser revogado a qualquer momento.
  5. SSH_PRIVATE_KEY: A chave privada SSH que foi gerada. Ela é a credencial para autenticar e obter acesso de escrita no repositório de manifestos (hello-manifests), permitindo que o GitHub Actions faça commits e pushs das alterações.
  6. SSH_PRIVATE_KEY.pub: Extensão púiblica da chave SSH com permissão para escrita no repositório de manifestos (hello-manifests).
  7. MANIFESTS_REPO_URL: O URL do seu repositório de manifestos, que o GitHub Actions usa para fazer a clonagem.
  8. MANIFESTS_TOKEN: É um nome que pode ser dado a um token de acesso pessoal do GitHub para autenticar o push para o repositório de manifestos, sendo uma opção válida quanto à chave SSH





