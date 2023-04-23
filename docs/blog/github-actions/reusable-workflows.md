# A Importância de reuso de workflows

Hoje vamos falar um pouco sobre o reuso de workflows, e como isso pode ser importante para você que está começando a utilizar o Github Actions.

Falamos anteriormente sobre o que são workflows, e como podemos utilizá-los para automatizar tarefas. Mas, e se você precisar executar uma tarefa em vários repositórios? Você iria copiar e colar o mesmo workflow em todos os repositórios? Ou, você iria criar um repositório apenas para armazenar os workflows?

Se você escolheu a primeira opção, você está fazendo um trabalho desnecessário, e se você escolheu a segunda opção, você está fazendo um trabalho desnecessário, e ainda está desperdiçando espaço no Github.

Então, como podemos resolver esse problema? A resposta é simples: __reutilizando workflows__!

## Reuso de workflows

O Github Actions permite que você crie workflows reutilizáveis, e que possam ser utilizados em qualquer repositório. Para isso, você deve criar um repositório apenas para armazenar os workflows, e referenciar em seu repositório de aplicação, IAC, etc.

### Criando um repositório para armazenar os workflows

Para criar um repositório para armazenar os workflows, você deve criar um repositório no Github, e adicionar um arquivo chamado `main.yml` na pasta `.github/workflows`. Esse arquivo deve conter o workflow que você deseja reutilizar.

Dentro da minha organization, eu tenho um repositório que realiza um build, publish e upload do artefato no github action para uma aplicação em dotnet. Nele é possível ver o uso de [reusable workflows](https://github.com/leandro-melloni/reusable-workflow-dotnet/blob/main/.github/workflows/build.yml).

Para utilizar um reusable workflow, você deve seguir alguns passos:

???- tip "Habilitar as actions e workflows."

    * Para habilitar as actions, você deve ir em `Settings > Actions > Actions Permissions` e selecionar a opção `Allow all actions and reusable workflows`.
    * Para habilitar os workflows, você deve ir em `Settings > Actions > Workflows Permissions` e selecionar a opção `Read Repository contents and packages permissions`.

???- tip "Dentro do `main.yml`, deve seguir o seguinte formato:"

    ```yaml
    name: Nome do reusable workflow

    on:
      workflow_call:
        inputs: # Inputs do workflow
        secrets: # Secrets do workflow

    jobs:
        job-name:
          runs-on: ubuntu-latest
          steps:
            - run: echo 'Meu reusable workflow!'
    ```

    * __workflow_call__: Conforme falamos anteriormente, o workflow no __github actions__ é executado quando um evento é disparado, e o __workflow_call__ é um evento que é disparado quando um workflow é chamado.
    * __inputs__: Inputs são variáveis que podem ser passadas para o workflow, e que podem ser utilizadas dentro do workflow.
    * __secrets__: Secrets são variáveis que podem ser passadas para o workflow, e que podem ser utilizadas dentro do workflow, porém, elas são mais seguras, pois não são expostas no log do workflow.

### Referenciando o repositório de workflows

Para referenciar o repositório de workflows, você deve adicionar o seguinte código no seu workflow principal:

```yaml
- uses: owner/repository@ref
```

Muito parecido com uma refência a uma action, não é mesmo? A diferença é que, ao invés de utilizar o nome da action, você deve utilizar o nome do repositório juntamente com o nome da organization, o __@__ logo a frente, e a __ref__ que você deseja utilizar, ela pode ser uma __branch__ ou um __commit__ ou até mesmo uma __tag__.

Alguns casos de uso, é possível testar um __reusable workflow__ em uma `feature branch` para testes e depois fazer o merge para a branch principal, criando uma tag e até mesmo um __release notes__ com as novas funcionalidades.

### Nested workflows

Da mesma forma que um __reusable workflow__, você pode chamar outro __reusable__ dentro deste __reusable workflow__, e assim por diante, isso se chama __nested workflows__.

Para utilizar workflows dentro de workflows, você deve adicionar o seguinte código no seu __reusable workflow__ principal:

```yaml
name: Nome do nested workflow
on:
  workflow_call:
    inputs: # Inputs do workflow
    secrets: # Secrets do workflow
jobs:
    job-name:
      runs-on: ubuntu-latest
      steps:
        - run: echo 'Meu nested workflow!'
        - uses: owner/reusable-workflow@ref
```

!!! warning "Atenção!"

    * O Github Actions não permite que você utilize workflows dentro de workflows, se o workflow principal estiver em um repositório __privado__.
    * O Nested Workflow permite que você reutilize um total de __20 workflows__ dentro de um mesmo arquivo.
    * É possível ter um total de __4 níveis de workflows__ a serem chamados.

## Conclusão

Neste artigo, vimos como podemos reutilizar workflows, e como isso pode ser importante para você que está começando a utilizar o Github Actions.

Quando falamos em __DevOps__, a reutilização é muito importante, seja para um profissional que acaba de chegar ao mercado, quanto para um profissional experiente, criando padrões e sem a necessidade de escrever várias vezes o mesmo código. Por isso o Github trouxe essa funcionalidade para o Github Actions, para que possamos reutilizar workflows, e assim, facilitar a vida de todos.

## Referências

- [Github Actions: Reusing workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows)
- [Github Actions: Nested workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows#nesting-reusable-workflows)