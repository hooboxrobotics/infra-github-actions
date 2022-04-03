# Hoobox - GitHub Actions

Repositório que centraliza as GitHub Actions que serão consumidos por todos os repositórios.

## Fazendo deploy com este repositório

As rotinas automáticas aqui separam o processo de _Continuous Integration_ de _Continuous Delivery_, e a junção das duas rotinas é o que o mercado chama de CI/CD.

O processo de _Continuous Integration_ consiste em testar e tornar disponível para um processo de deploy uma versão de uma aplicação. Como as aplicações da Hoobox são servidas por meio de containers, este processo culmina na entrega de um container no Container Registry da Hoobox.

O processo de _Continuous Delivery_, por sua vez, se encarrega de entregar este container em execução no cluster de Kubernetes da Hoobox, substituindo qualquer outro container com uma versão anterior da aplicação.

Abaixo, um exemplo de como chamar o workflow de CI/CD deste repositório a partir de outro:

```yaml
name: build and push container

on:
  push:
    branches:
    - "develop"
    - "main"

jobs:
  cicd:
    uses: hooboxrobotics/infra-github-actions/.github/workflows/cicd.yaml@main
    with:
      run_ci: true
      run_cd: true
    secrets:
      ...
```

Note que você pode controlar quais etapas deseja que sejam realizadas por meio das variáveis `run_ci` e `run_cd`.


## Dependência do Helm

Esta rotina foi criada com o intuito de, em última instância, chamar para o processo de _Continuous Deploy_ o repositório [infra-helm-apps](https://github.com/hooboxrobotics/infra-helm-apps).

Futuramente, será dado suporte à repositórios com Helm Charts genéricos, e as variáveis [Secrets](#secrets) individuais serão substituídas por um JSON com suporte à valores dinâmicos.

## Secrets 

O correto funcionamento deste repositório depende da declaração das seguintes _Secrets_:

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

