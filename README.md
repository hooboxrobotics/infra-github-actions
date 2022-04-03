# Hoobox - GitHub Actions

Repositório que centraliza as GitHub Actions que serão consumidos por todos os repositórios.

As rotinas automáticas aqui separam o processo de _Continuous Integration_ de _Continuous Delivery_. 

Basicamente, o primeiro (CI) irá se encarregar de testar e _buildar_ a aplicação, visando tornar disponível uma versão da mesma para posterior deploy, enquanto que o segundo (CD) efetivamente faz o deploy da versão que foi criada pelo CI.

Para correto funcionamento, exige que o repositório onde a Action será executada possua as seguintes _secrets_:

|                 Variável|  Tipo  |Descrição     |
|------------------------:|:------:|:-------------|
|     `APP_CREATE_INGRESS`|Booleana|Indica que um novo _Ingress_ deve ser criado|
|  `APP_MEMCACHED_ENABLED`|Booleana|Indica que o app usa Memcached|
|      `APP_MINIO_ENABLED`|Booleana|Indica que o app usa um object storage intermediado pelo min.io|
|      `APP_MYSQL_ENABLED`|Booleana|Indica que o app usa MySQL e portanto deve ter um banco relacionado|
|`CONTAINER_REGISTRY_HOST`| String |O _host_ de onde a imagem do container será baixado|
|`CONTAINER_REGISTRY_PASS`| String |A senha para acessar o Registry e baixar a imagem|
|`CONTAINER_REGISTRY_USER`| String |O usuário para acessar o Registry e baixar a imagem|
|          `GIT_HELM_REPO`| String |O repositório de onde será baixado o [Helm Chart](https://github.com/hooboxrobotics/infra-helm-apps) para deploy|
|       `GIT_HELM_SSK_KEY`| String |A chave de SSH (somente leitura) para realizar o download (git clone) do repositório com os Helm Charts|
|             `KUBECONFIG`| String |**Deprecated** Conteúdo do arquivo kubeconfig para acesso ao Kubernetes|
|      `KUBERNETES_CONFIG`| String |Conteúdo do arquivo kubeconfig para acesso ao Kubernetes|

Além disto, o repositório que estiver reutilizando as rotinas aqui existentes deve conter também um workflow semelhante à este abaixo:

```yaml
name: build and push container

on:
  push:
    branches:
    - "develop"
    - "main"
  workflow_dispatch:

jobs:
  cicd:
    uses: hooboxrobotics/infra-github-actions/.github/workflows/cicd.yaml@main
    with:
      run_ci: true
      run_cd: true
    secrets:
      KUBECONFIG: ${{ secrets.KUBECONFIG }}
      GIT_HELM_REPO: ${{ secrets.GIT_HELM_REPO }}
      GIT_HELM_SSK_KEY: ${{ secrets.GIT_HELM_SSK_KEY }}
      APP_CREATE_INGRESS: ${{ secrets.APP_CREATE_INGRESS }}
      CONTAINER_REGISTRY_USER: ${{ secrets.CONTAINER_REGISTRY_USER }}
      CONTAINER_REGISTRY_PASS: ${{ secrets.CONTAINER_REGISTRY_PASS }}
      CONTAINER_REGISTRY_HOST: ${{ secrets.CONTAINER_REGISTRY_HOST }}

```
