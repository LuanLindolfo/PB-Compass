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

  ### Funcionamento da etapas de processos
  - O GithubActions detecta um novo commit no repositório *hello-app*, acionando assim o pipeline do CI/CD
  - Uma imagem é criada no Docker e publicada no DockerHub
  - Em seguida, o arquivo deployment.yaml é atualizado no repositório *hello-manifests*
  - Toda a atualização é monitorada pelo ArgoCD, basta sincronizar na aplicação criada no ArgoCD

