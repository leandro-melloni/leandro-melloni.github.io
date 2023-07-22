# Actions

Nas últimas semanas falamos sobre workflows, seus casos de uso e a importância de reutilizar workflows, hoje vamos falar sobre as actions.

## O que são actions?

Actions são os passos que compõem um workflow, e elas podem ser executadas em qualquer ambiente, seja ele local ou na nuvem, e podem ser escritas em qualquer linguagem de programação, como por exemplo, __JavaScript__, __TypeScript__, __Python__, etc. Ela pode ser usada a partir do __MarketPlace__ do Github, ou criada por você mesmo para casos em que a comunidade não tenha publicado anteriormente ou alguma demanda interna em sua __organização__.

!!! warning "Relembrando!"
    Você deve utilizar o comando `uses` dentro de um `step`, conforme o exemplo abaixo:

    ```yaml	
    steps:
      - uses: actions/checkout@v2
    ```

Para casos que a action não esteja disponível no __MarketPlace__, você pode referenciar diretamente em um repositório privado, conforme o exemplo abaixo:

```yaml
steps:
  
  - name: Checkout
    uses: actions/checkout@v3
    with:
      repository: leandro-melloni/exec-terraform # nome do repositório dentro da organização
      path: .github/actions/exec-terraform # caminho da action dentro do repositório
      ref: refs/heads/main # branch do repositório
      token: ${{ steps.my-app.outputs.token }} # token de acesso ao repositório
  - uses: ./.github/actions/exec-terraform
```

## Como criar uma action?

Para criar uma action, você deve criar um repositório público (ou internal, caso sua Organization seja privada) no Github, e adicionar um arquivo chamado `action.yml` na raiz de seu repositório.

!!! warning "Atenção!"
    O nome do arquivo deve ser `action.yml`, e não `action.yaml`.

O arquivo `action.yml` deve conter as seguintes informações:
* `name`: Nome da action.
* `description`: Descrição da action.
* `inputs`: Inputs da action.
* `outputs`: Outputs da action.
* `runs`: Define como a action será executada.

```yaml	
name: Nome da action # Obrigatório para publicar no MarketPlace
description: Descrição da action # Obrigatório para publicar no MarketPlace
branding:
  icon: Nome do ícone da action # Obrigatório para publicar no MarketPlace
  color: Cor da action # Obrigatório para publicar no MarketPlace
inputs: # Inputs da action
outputs: # Outputs da action
runs: # Define como a action será executada
```

!!! warning "Atenção!"
    * O nome da action deve ser único, e não pode conter espaços.
    * O nome da action deve conter no máximo 255 caracteres.
    * A descrição da action deve conter no máximo 255 caracteres.
    * O nome da action deve ser escrito em __lowercase__.
    * O nome da action deve conter apenas letras, números, __-__ e __.__.
    * O nome da action deve começar com uma letra ou número.
    * O nome da action não pode terminar com __-__ ou __.__.
    * O nome da action não pode conter __-__ ou __.__ consecutivos.
    * O nome da action não pode ser __.`github`__ ou __.`git`__.
    * O nome da action não pode começar com __.`-`__.

### Inputs

Os inputs são os parâmetros que serão passados para a action, e eles podem ser definidos no arquivo `action.yml` da seguinte forma:

```yaml
inputs:
  input1:
    description: Descrição do input1
    required: true
    default: Valor padrão do input1
```

### Outputs

Os outputs são os valores que serão retornados pela action, e eles podem ser definidos no arquivo `action.yml` da seguinte forma:

```yaml
outputs:
  output1:
    description: Descrição do output1
```

### Runs

O `runs` define como a action será executada, e ele pode ser definido no arquivo `action.yml` da seguinte forma:

```yaml
runs:
  using: docker
  image: Dockerfile
```

Dentro do `runs` é possível escolher 3 opções:

* __docker__: Executa a action em um container Docker.
    - __image__: Define a imagem Docker que será utilizada.
    - __args__: Define os argumentos que serão passados para a imagem Docker.
* __node16__: Executa a action em um ambiente Node.js.
    - __main__: Define o arquivo JavaScript que será executado.
    - __pre__: Define o arquivo JavaScript que será executado antes do arquivo definido em `main`.
    - __post__: Define o arquivo JavaScript que será executado depois do arquivo definido em `main`.
* __composite__: Executa a action em um ambiente composto.
    - __steps__: Define os passos que serão executados.

!!! warning "Atenção!"
    * O `runs` é obrigatório.
    * O uso do `composite` pode ser substituído pelo __reusable workflows__ ou __nested workflows__.
    * O uso do `node16` será executado diretamente dentro do seu runner, e não em um container Docker, ganhando performance na sua execução.
    * O uso do `docker` será executado em um container Docker, e não diretamente dentro do seu runner, sendo necessário o tempo de provisionamento do seu container em tempo de execução do seu workflow, perdendo assim um pouco de performance.

## Exemplo de action

O exemplo na URL anexa a este artigo, é uma action que executa comandos de terraform utilizando __JavaScript__. [exec-terraform](https://github.com/leandro-melloni/exec-terraform)

## Publicando uma action

Para publicar uma action, a partir do repositório criado nos passos anteriores, alguns passos são obrigatórios:

* Necessário adicionar a tag `v1` no seu release, e a partir daí, sua action estará disponível para uso.

* Em seu __README.MD__, você pode adicionar um exemplo de como utilizar sua action e qualquer informação que ache relevante para poder publicar no __MarketPlace__.

* Adicionar a __Primary Category__ e a __Secondary Category__ em seu repositório, para que sua action seja encontrada no __MarketPlace__.

* Seguir com o __Título__ da __Release__, seguindo sempre os padrões de versionamento do __SemVer__.

* Dentro da __Descrição__ da __Release__, adicionar o __Changelog__ da sua action, seguindo sempre os padrões de versionamento do __SemVer__, se possível, adicionar o __Changelog__ em __Markdown__.

!!! warning "Atenção!"
    * O nome da tag deve ser `v1`.
    * A tag deve ser adicionada no seu release, e não no seu commit.

## Conclusão

Caso você possua alguma necessidade interna de sua empresa, ou até mesmo uma necessidade pessoal, você pode criar uma action para resolver seu problema, e publicar no __MarketPlace__ para que outras pessoas possam utilizar sua action.

Em casos de necessidades internas, você pode criar uma action privada, e utilizar em seus workflows, sem a necessidade de publicar no __MarketPlace__.

O uso de actions, pode ser muito útil para automatizar tarefas repetitivas, e até mesmo para criar um workflow mais limpo e organizado.

## Referências

* [Github Actions - Criando uma action](https://docs.github.com/en/actions/creating-actions)


