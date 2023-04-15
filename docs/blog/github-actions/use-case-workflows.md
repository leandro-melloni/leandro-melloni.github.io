# Casos de Uso de Workflows

Nas últimas semanas eu trouxe um pouco sobre o que são os workflows e como eles funcionam. Agora, vamos falar um pouco sobre os casos de uso de workflows.

## Caso de Uso 1: Build

O primeiro caso de uso que eu vou falar é o __build__. O build é um processo que é feito para compilar o código fonte de um projeto e gerar um artefato. Esse artefato pode ser um executável, um pacote, um arquivo de configuração, etc.

Quando falamos em uma automação de um build, podemos pensar em um cenário onde o código fonte é enviado para um repositório, e quando isso acontece, o workflow é disparado e o build é feito. O resultado do build é então enviado para outro repositório, que pode ser um repositório de artefatos, ou um repositório de releases.

Este build pode ser uma __linguagem de programação__, como Java, C#, Python, etc. Ou pode ser um __container__, como Docker, etc.

Seguem alguns casos de uso de build:

???- tip "Build Dotnet"
    ```yaml
    name: Build C#
    on:
      push:
        branches:
          - main
    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v3
        - name: Setup .NET Core
          uses: actions/setup-dotnet@v1 # Action criada pela Github para instalação do .NET
          with:
            dotnet-version: 7.0.x
        - name: Restore
          run: dotnet restore --verbosity normal
        - name: Build
          run: dotnet build --configuration Release
        - name: Publish
          run: dotnet publish --configuration Release --no-build --output ./artifacts
        - name: Upload artifacts
          uses: actions/upload-artifact@v2
          with:
            name: artifacts
            path: ./artifacts
    ```
???- tip "Build Docker"
    ```yaml
    name: Build Docker
    on:
      push:
        branches:
          - main
    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v3
        - name: Download Artifacts
          uses: actions/download-artifact@v2
          with:
            name: artifacts
            path: ./artifacts
        - name: Build
          run: docker build -t leandromelloni/leandro-melloni.github.io:latest .
        - name: Login
          uses: docker/login-action@v1
          with:
            username: ${{ secrets.DOCKER_USERNAME }}
            password: ${{ secrets.DOCKER_PASSWORD }}
        - name: Push
          run: docker push leandro-melloni/leandro-melloni.github.io:latest
    ```

Os dois casos acima são exemplos de build. O primeiro é um build de um projeto .NET, e o segundo é um build de um projeto Docker e ambos são enviados para um repositório de artefatos dentro do próprio Github, que é o __Github Packages__.

## Caso de Uso 2: Test

O segundo caso de uso que eu vou falar é o __test__. O test é um processo que é feito para testar o código fonte de um projeto. Esse teste pode ser um teste unitário, um teste de integração, um teste de aceitação, um teste de carga, um teste de performance, um teste de segurança, etc.

Cada linguagem de programação tem uma forma de fazer testes, e cada framework de testes tem uma forma de fazer testes. Por isso, o teste pode ser feito de várias formas, e isso depende muito do projeto.

Um teste unitário pode ser feito com o __xUnit__, o __NUnit__, o __Junit__, etc. Um teste de integração pode ser feito com o __Selenium__, o __Cypress__, o __Postman__, etc. Um teste de aceitação pode ser feito com o __Cucumber__, o __SpecFlow__, etc. Um teste de carga pode ser feito com o __JMeter__, o __Gatling__, etc. Um teste de performance pode ser feito com o __JMeter__, o __Gatling__, etc. Um teste de segurança pode ser feito com o __OWASP ZAP__, o __OWASP Dependency Check__, etc.

Seguem alguns casos de uso de test:

???- tip "Test Dotnet"
    ```yaml
    name: Test C#
    on:
      push:
        branches:
          - main
    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v3
        - name: Setup .NET Core
          uses: actions/setup-dotnet@v1 # Action criada pela Github para instalação do .NET
          with:
            dotnet-version: 7.0.x
        - name: Restore
          run: dotnet restore --verbosity normal
        - name: Test
          run: dotnet test --configuration Release --verbosity normal
    ```

???- tip "Sonar Test"
    ```yaml
    name: Sonar Test
    on:
      push:
        branches:
          - main
    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v3
        - name: Setup .NET Core
          uses: actions/setup-dotnet@v1 # Action criada pela Github para instalação do .NET
          with:
            dotnet-version: 7.0.x
        - name: Restore
          run: dotnet restore --verbosity normal
        - name: Test
          run: dotnet test --configuration Release --verbosity normal
        - name: Sonar
          uses: SonarSource/sonarcloud-github-action@master
          with:
            projectBaseDir: .
            organization: leandromelloni
            token: ${{ secrets.SONAR_TOKEN }}
    ```

Os dois casos acima são exemplos de test. O primeiro é um test de um projeto __.NET__, e o segundo é um test de um projeto .NET e um teste de segurança com o __SonarCloud__.

## Caso de Uso 3: Deploy

O terceiro caso de uso que eu vou falar é o __deploy__. O deploy é um processo que é feito para implantar o código fonte de um projeto em um determinado ambiente. Esse ambiente pode ser de desenvolvimento, homologação ou produção, etc.

Algo muito interessante quando falamos de deploy é que podemos fazer o deploy de várias formas. Podemos fazer o deploy de uma aplicação __web__, __mobile__, __desktop__, __container__, etc.

Além do tipo de aplicação, podemos pensar também onde essa aplicação vai ser implantada. Podemos implantar em um __servidor físico__, em um __servidor virtual__, em um __servidor em nuvem__, em um __cluster de containers__, etc.

Seguem alguns casos de uso de deploy:

???- tip "Deploy AWS"
    ```yaml
    name: Deploy AWS
    on:
      push:
        branches:
          - main
    jobs:
      deploy:
        runs-on: ubuntu-latest
        steps:
        - uses: actions/checkout@v3
        - name: Deploy
          uses: aws-actions/configure-aws-credentials@v1
          with:
            aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
            aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
            aws-region: us-east-1
        - name: Login
          uses: docker/login-action@v1
          with:
            username: ${{ secrets.DOCKER_USERNAME }}
            password: ${{ secrets.DOCKER_PASSWORD }}
        - name: Docker pull
          run: docker pull leandromelloni/leandro-melloni.github.io:latest
        - name: Deploy ECR
          run: |
            aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com
            docker tag leandromelloni/leandro-melloni.github.io:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/leandro-melloni.github.io:latest
            docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/leandro-melloni.github.io:latest
        - name: Fill in the new image ID in the Amazon ECS task definition
          id: task-def
          uses: aws-actions/amazon-ecs-render-task-definition@v1
          with:
            task-definition: task-definition.json
            container-name: leandro-melloni
            image: 123456789012.dkr.ecr.us-east-1.amazonaws.com/leandro-melloni.github.io:latest
        - name: Deploy ECS
          uses: aws-actions/amazon-ecs-deploy-task-definition@v1
          with:
            task-definition: ${{ steps.task-def.outputs.task-definition }}
            service: leandro-melloni
            cluster: leandro-melloni
            wait-for-service-stability: true
    ```

O caso acima é um exemplo de deploy de uma aplicação __container__ em um __serviço de container__ na __AWS__ chamado __ECS__.

## Juntando os casos de uso

Agora que você já conhece os casos de uso, vamos juntar os três casos de uso que eu falei e criar um workflow completo.

O workflow que eu vou criar é o seguinte:

* Quando um desenvolvedor fizer um __push__ no __branch__ __main__, o __workflow__ vai ser executado.
* O __workflow__ vai fazer o __build__ do DotNET do projeto e em paralelo vai executar o __test__ também em DotNET.
* Quando o __build__ e o __test__ terminarem, o __workflow__ vai fazer o __build__ do __docker__.
* Quando o __build__ do __docker__ terminar, o __workflow__ vai fazer o __deploy__ do __docker__ no __ECS__. (Devido a ausência de um __ECS Cluster__, o deploy não vai funcionar, mas o __workflow__ vai ser executado com sucesso a partir de um echo de teste.)

Segue o workflow completo no repositorio do Github: [clique aqui](https://github.com/leandro-melloni/use-case-workflows).

O repositório acima se encontra em minha organização pessoal do Github, nele eu irei postar vários exemplos de casos de uso e será utilizado para artigos futuros.


## Conclusão

Neste artigo eu mostrei alguns casos de uso de __workflows__. Eu mostrei como podemos fazer __build__, __test__ e __deploy__ com o __Github Actions__ e quais são as possibilidades que temos para fazer esses processos, usando actions providos pela própria __Github__ ou actions criados por __terceiros__, dos quais podem vir da própria __AWS__ ou criadas pela própria comunidade.

Eu também mostrei como podemos juntar esses casos de uso para criar um __workflow__ completo.

## Referências

* [Github Actions](https://docs.github.com/pt/actions)
* [Github Actions Marketplace](https://github.com/marketplace)
* [Criar e testar .NET](https://docs.github.com/pt/actions/guides/building-and-testing-net)
* [Empacotamentos com Github Actions](https://docs.github.com/pt/actions/publishing-packages/about-packaging-with-github-actions)
* [Empacotando e publicando um contêiner](https://docs.github.com/pt/actions/guides/publishing-docker-images)