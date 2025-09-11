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

## Objetivo
Executar um conjunto de microserviços (Online Boutique) em Kubernetes local usando Rancher Desktop, controlado por GitOps com ArgoCD, a partir de um repositório público no GitHub. 

# Fork e repositório GitHub

Para o acesso aos microserviços da aplicação, foi realizado um fork (bifurcação) a partir do resporitório público [microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo/). O foco é no arquivo YAML com o seguinte caminho: *release/kubernetes-manifests.yaml*
O fork tem o objetivo de propor mudanças em um código de outra pessoa, a partir da bifurcação, a adaptações podem ser realizadas. Neste caso, foi utilizad o git para fazer o fork localmente.

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
