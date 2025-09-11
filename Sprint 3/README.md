# Gitops
## Sumário
- [Objetivo](/Objetivo)

## Tecnologias Utilizadas 💻
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)
![YAML](https://img.shields.io/badge/YAML-CB171E?style=for-the-badge&logo=yaml&logoColor=white)
![Rancher](https://img.shields.io/badge/Rancher-0075A8?style=for-the-badge&logo=rancher&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![ArgoCD](https://img.shields.io/badge/ArgoCD-EF7420?style=for-the-badge&logo=argo&logoColor=white)
![Git](https://img.shields.io/badge/Git-F05032?style=for-the-badge&logo=git&logoColor=white)

## Objetivo 🎯
Executar um conjunto de microserviços (Online Boutique) em Kubernetes local usando Rancher Desktop, controlado por GitOps com ArgoCD, a partir de um repositório público no GitHub. 

### O que é GitOps?
GitOps utiliza a base DevOps (containerização, gerenciamento e ambientação em nuvem) utilizando o Git como única fonte para estruturação e aplicação. A prática desse conceito será reconhecida no atual projeto.

# Fork e repositório GitHub

Para o acesso aos microserviços da aplicação, foi realizado um fork (bifurcação) a partir do resporitório público [microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo/). O foco é no arquivo YAML com o seguinte caminho: *release/kubernetes-manifests.yaml*
O fork tem o objetivo de propor mudanças em um código de outra pessoa, a partir da bifurcação, a adaptações podem ser realizadas. Neste caso, foi utilizad o git para fazer o fork localmente. o fork realizado a partir do repositório público se encontra no caminho [microservices-demo-fork](https://github.com/LuanLindolfo/microservices-demo)

## Instação do git e realização do fork
A instalação do git foi realizada a partir da documentação do GitDocs para [Configuração do git](https://docs.github.com/pt/get-started/git-basics/set-up-git) que está disponível para Windows, Mac e Linux/Unix, no caso desta aplicação, foi utilizado o git para o sistema operacional Windows.

Após a instalação disponível na [Configuração do git](https://docs.github.com/pt/get-started/git-basics/set-up-git), foi escolhido o nome de usuário e a senha bem como foi feito o link como github para o pull e o push do fork.

Os comandos utilizados inicialmente também foram advindos do GitDocs, na seção de [Criação de fork de um repositório](https://docs.github.com/pt/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo) onde:
  - Houve navegação até o diretório [microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo/)
  - Foi selecionada a opção de fork (no canto direito superior)
   <img width="482" height="45" alt="Image" src="https://github.com/user-attachments/assets/39a2b691-6663-443d-94b1-9fc9b54c727e" />
  - Em seguida, basta editar o nome do repositório onde por padrão são nomeados da mesma forma que o repositório de origem do fork.
  - É importante copiar apenas o branch main para obter apenas o ramo principal do código.

Ao ser criada a bifurcação, tem que ser clonado o repositório de origem pois mesmo que tenha sido bifurcado, é importante ter os arquivos localmente (para fins de edição e outras ações a serem tomadas a partir do repositório), dessa forma foi utilizado o seguinte esquema com o Github e o Git:
  - Na seleção "Code" do projeto, copie o HTTPS do projeto
  - No gitbash insira o comando, no local onde os arquivos deverão ser clonados, ``` git clone ``` com as informações copiadas:
    ```
    git clone https://github.com/username/repositório
    ```
    Será mostrada uma resposta nessa estrutura:
    ```
    $ git clone https://github.com/username/repositório
    > Cloning into `(repositório)`...
    > remote: Counting objects: 10, done.
    > remote: Compressing objects: 100% (8/8), done.
    > remote: Total 10 (delta 1), reused 10 (delta 1)
    > Unpacking objects: 100% (10/10), done.
    ```
  - Com o mesmo HTTPS copiado, após o primeiro comando no gitbash e no diretório em que o fork está, sincronize o for com o repositório upstream (origem), utilize o comando:
    ```
    git remote -v
    ```
    Dessa forma, irá aparecer uma resposta como:
    ```
    origin  https://github.com/username/seu-fork.git (fetch)
    origin  https://github.com/username/seu-fork.git (push)
    ```
  - Adicione a conexão remota com o upstream para a bifurcação:
    ```
    git remote add upstream https://github.com/proprietário-original/repositório-original.git
    ```
    Ao inserir o comando novamente, será possível ver o repositório. A resposta ao comando será nessa estrutura:
    ```
    $ git remote -v
    > origin    https://github.com/username/seu-fork.git (fetch)
    > origin    https://github.com/username/seu-fork.git (push)
    > upstream  https://github.com/proprietário-original/repositório-original.git (fetch)
    > upstream  https://github.com/proprietário-original/repositório-original.git (push)
    ```

    Dessa forma, a bifurcação será mantida sincronizada com o repositório upstream (de origem).
## Kubernetes, cluster e pods

É essencial saber que, por se tratar de um subsistema, é importante estar no diretório correto para a instalação das dependências de Kubectl, Minikube e ArgoCD, nese caso, foi utilizado o caminho ``` cd /mnt/c/Users/SeuUsuario/Restante_do_caminho_da_aplicação ```.

Para a estruturação de kubernetes na aplicação, foram utilizadas as seguintes ferramentas:
1. [Ubuntu 24.04.1 LTS (WSL)](https://apps.microsoft.com/detail/9NZ3KLHXDJP5?hl=neutral&gl=BR&ocid=pdpshare)
2. [Rancher Desktop (Para executar e gerir cluster localmente)](https://docs-rancherdesktop-io.translate.goog/getting-started/installation/?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc)
3. [Kubectl (Gerenciador de cluster Kubernetes)](https://kubernetes-io.translate.goog/docs/tasks/tools/install-kubectl-linux/?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc#download-binary-linux-0)
4. [Minikube (Ferramenta de execução de cluster local)](https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Fbinary+download)
5. [ArgoCD (Ferramenta de entrega contínua para Kubernetes)](https://argo--cd-readthedocs-io.translate.goog/en/stable/getting_started/?_x_tr_sl=en&_x_tr_tl=pt&_x_tr_hl=pt&_x_tr_pto=tc)

Lembrando que é importante ter acesso à internet para o download das pendências necessárias para a aplicação (como imagens e as devidas ferramentas), o teste de conectividade pode ser feito por meio do ping com o servidor do google, que retornará o envio e a verificação de pacotes de dados, com o comando:
```bash
  ping google.com
```

  A instalação de cada ferramenta encontra-se linkada com as documentações e store disponível de download. É importante que a sequência de instalação seja realizada na ordem estabelecida para o funcionamento correto da aplicação GitOps.

  Enfatizando a aplicação do minikube e do ArgoCD,temos a personalização de aplicação para consumo de cada CPU disponibilizada para o cluster, neste caso, para o bom funcionamento do cluster foi utilizado o seguinte comando para start do minikube:
  ```bash
  minikube start --driver=docker --cpus=2 --memory=2048mb
  ```
  - ``` minikube start ```: Comando que inicia o minikube criando um cluster localmente.
  - ``` --driver=docker ```: Especifica que o cluster irá ser executado em um container, no caso, estabelece o driver que o minikube usa para o ambiente (docker).
  - ``` --cpus=2 ```: Define a quantidade de CPU virtual para o o cluster, em caso de ocorrer problema em uma, haverá disponibilidade de outra CPU virtual.
  - ``` --memory=2048mb ```: Especifica a quantidade de memória alocada para o cluster, neste caso, 2GB.

Em seguida, haverá a construção da estrutura para a aplicação do cluster que deve levar um curto período de tempo a depender de cada máquina e da personalização do start, sendo possível verificar os pods e os namespaces por meio do comando:
  ```bash
  minikube kubectl -- get pods -A
  ```
  - ``` minikube kubectl ```: Comando que inicia o kubectl por meio do minikube comunicando-se com o cluster localmente.
  - ``` -- get pods ```: Comando que lista pods.
  - ``` -A ```: Abreviação do comando ``` --all-namespaces ``` busca todos os pods em todos os namespaces.

O resultado da aplicação está bem sucedida quando estiver no padrão:
  ```bash
NAMESPACE     NAME                               READY   STATUS    RESTARTS      AGE
kube-system   coredns-66bc5c9577-gbf6h           1/1     Running   0             43s
kube-system   coredns-66bc5c9577-z527g           1/1     Running   0             43s
kube-system   etcd-minikube                      1/1     Running   0             50s
kube-system   kube-apiserver-minikube            1/1     Running   0             50s
kube-system   kube-controller-manager-minikube   1/1     Running   0             49s
kube-system   kube-proxy-z62wd                   1/1     Running   0             44s
kube-system   kube-scheduler-minikube            1/1     Running   0             50s
kube-system   storage-provisioner                1/1     Running   1 (10s ago)   48s
  ```
### Acessando o ArgoCD localmente
 Após a aplicação dos pods e dos namespaces em conjunto da construção do cluster, será realizada a aplicação do ArgoCD. A ferramenta tem como objetivo utilizar o modelo GitOps como modelo de aplicação, verificando, sincronizando e atualizando as alterações do repositório (única fonte). Os passos foram os seguinte:
 1. Criando o nomespace argocd
     ```bash
    kubectl create namespace argocd
    ```
 2. Baixando o arquivo de instalação doArgoCD diretamente do repositório oficial do GitHub
     ```bash
    kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
    ```
     - ``` kubectl apply ```: Atualiza os recursos a partir da leitura de um arquivo ou URL
     - ``` -n argocd ```: Informando o namespace (em caso de não existir, o arquivo d einstalação instalará)
     - ``` -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml ```: Apontando o caminho do arquivo **install.yaml** YAML com os devidos recursos
 3. Verificação do processo
     ```bash
    kubectl get pods -n argocd
    ```
 4. Padrão de instalação correta
     ```bash
       NAME                                               READY   STATUS              RESTARTS   AGE 
     argocd-application-controller-0                    1/1     Running               0          17s 
     argocd-applicationset-controller-cb7958dd7-tlcv5   1/1     Running               0          17s 
     argocd-dex-server-6b45dfcbb8-456b5                 1/1     Running               0          17s 
     argocd-notifications-controller-85d8b54b6f-fc2rn   1/1     Running               0          17s 
     argocd-redis-6596588c7c-wnxn9                      1/1     Running               0          17s 
     argocd-repo-server-69777f45db-6f78r                1/1     Running               0          17s 
     argocd-server-577c86bbd8-nrm7z                     1/1     Running               0          17s
    ```
5. Acessando o ArgoCD com o comando que direciona o tráfego do serviço argocd-server para a porta 8080 para a máquina na porta 443
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
    ```
7. Acessando pelo localhost
   ```bash
   https://localhost:8080
    ```
  - Para acessar a aplicação basta utilizar o comando para obter a senha:
     ```bash
     kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
      ```
     - ``` kubectl -n argocd get secret argocd-initial-admin-secret ```: Busca no namespace o recurso Secret ```argocd-initial-admin-secret```
     - ``` -o jsonpath="{.data.password} ```: Consultando e extraindo a saída filtrada apenas com o valor da chave
     - ``` | base64 -d ```: Usa a saída do comando anterior e encaminha para o próximo
  
  Por padrão, o usuário é    ```admin```
  
