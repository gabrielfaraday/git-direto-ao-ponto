<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/Git-logo.svg/1200px-Git-logo.svg.png" alt="logo" width="75"/>

# xpto-api

API responsável por XPTO, envolvendo os fluxos XYZ

Recebe mensagem via uma chamada HTTP e envia uma notificação para uma fila do do message broker

## How to use

Para chamar a API basta seguir a documentação do /swagger

### Message Broker

Para verificar as mensagens de notificação no message broker acesse: http://rabbitmq:15672, usuário e senha: 'guest'
 
#### >>> Notification Payload

A mensagem de notificação terá o seguinte payload:

```json
{
   "Status": "OK",
   "StatusCode": 200,
   "Transactions":[
      {
         "TransactionRef": "123",
         "Value": 10
      }
   ]
}
```

Considerações sobre o payload:

- `Status`: mensagem com texto explicando o retorno
- `StatusCode`: código que segue a logica de HTTP Status Code. Pode ter os seguintes valores:
  - `200`: transações realizadas com sucesso
  - `400`: a mensagem enviada possui alguma inconsistência
  - `422`: a operação não ocorreu por alguma regra
  - `502`: o limite máximo de retentativas foi atingido e a operação não ocorreu
- `Transactions`: em caso de statusCode 200, serão retornadas as transações solicitadas com o respectivo valor

Existem filas separadas para os ambientes Local (notification.local), Development (notification.dev) e QA (notification.qa)

## Technologies

- .NET 5.0 (WEB API)
- RabbitMQ
- MongoDB


## Getting started

Para rodar o projeto na máquina você precisa ter o [.NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0) instalado na máquina.

Clone o projeto para sua máquina e restaure os pacotes:

```shell
git clone https://github.com/gabrielfbarros/git-direto-ao-ponto.git
cd git-direto-ao-ponto/
dotnet restore
```
A aplicação está pronta para ser utilizada localmente.


### Building

Após alterar o código garanta que a aplicação está "buildando":

```shell
dotnet build
```


### Testing

Após alterar o código garanta que os testes da aplicação estão passando:

```shell
dotnet test //p:CollectCoverage=true //p:CoverletOutputFormat=OpenCover (para Windows)

----------------------------------------------------------------------------------------------------------

dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=OpenCover (para Linux)
```


**Code Coverage**

Para visualizar um relatório completo de code coverage instale a tool necessária, caso ainda não tenha instalado:

```shell
dotnet tool install -g dotnet-reportgenerator-globaltool
```

E gere o report:

```shell
reportgenerator "-reports:tests\Xpto\coverage.opencover.xml" "-targetdir:tests\Xpto\test-reports" "-sourcedirs:src/" -reporttypes:HTML (para Windows)

----------------------------------------------------------------------------------------------------------

reportgenerator "-reports:tests/Xpto/coverage.opencover.xml" "-targetdir:tests/Xpto/test-reports" "-sourcedirs:src/" -reporttypes:HTML (para Linux)

```

O report estará diposnivel em tests/Xpto/test-reports/index.html



### Running

Para rodar a aplicação em modo debug no Visual Code ou via CLI, substitua o conteúdo do arquivo .Properties/launchSettings.json, e pressione F5:

``` json

{
  "profiles": {
    "Xpto": {
      "commandName": "Project",
      "dotnetRunMessages": "true",
      "environmentVariables": {
        "DOTNET_ENVIRONMENT": "Local",
        "RABBITMQ_USERNAME": "guest",
        "RABBITMQ_PASSWORD": "guest"
      }
    }
  }
}

```


Para rodar a aplicação via [Docker](https://www.docker.com/products/docker-desktop) execute:

```shell
docker build . -t {nome-da-imagem}
docker run \
    -e DOTNET_ENVIRONMENT=Local \
    -e RABBITMQ_USERNAME=guest \
    -e RABBITMQ_PASSWORD=guest \
    {nome-da-imagem}
```

**Environment Variables**

- DOTNET_ENVIRONMENT: Indica o ambiente que a aplicação deve rodar (Development, QA, UAT, etc)
- RABBITMQ_USERNAME: Indica o usuário para acesso ao RabbitMQ
- RABBITMQ_PASSWORD: Indica a senha para acesso ao RabbitMQ


### Deploying

O deploy ocorre de forma automática para o ambiente de develop, basta fazer o merge das alterações para a branch develop no Gitlab e o pipeline irá fazer a integração e deploy do seu código:

```shell
git add -A
git commit -m "sua msg aqui"
git push origin {branch_desejada}
```

Para verificar o progresso do pipeline clique [aqui](http://google.com).


## Sonar

Ao rodar o pipeline do Gitlab diversas métricas de qualidade de código serão coletadas pelo Sonar. Verifique o resultado [aqui](http://google.com).


## Swagger

Ao rodar local o swagger da aplicação estará disponivel em http://localhost:5000/


## Health Check

Ao rodar local o health check da aplicação estará disponivel em http://localhost:5000/health


## Links

- [Documentação](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)