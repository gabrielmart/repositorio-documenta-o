# API-TIRA-DUVIDA-CHATGPT-KENLO

API responsável por enviar uma mensagem do usuário ao ChatGPT e retornar uma resposta 

<BR>


## Arquitetura
---


Esse projeto foi desenvolvido em camadas logicas, visando colocar em praticas os principios do SOLID, a modelagem de Dominio Rico utilizado em DDD e alguns Designs Patterns.

<BR>

Abaixo será realizado uma explicação sobre cada camada projeto:

<BR>

### Models
---
Nessa camada estão as classe responsáveis por representar os modelos/entidades da aplicação. Em cada classe foi utilizado a modelagem de Domínio Rico, possibilitando que todas as operações de dados e regras de dominio sejam tratadas diretamente nas classes modelo, evitando a falta de consistência que acontece muitas vezes quando atribuimos essa responsabilidade de validação a uma camada de service, no qual cada service realiza um tipo de validação aos dados da classe modelo.

Juntamente, foi aplicado o conceito de **Single Responsibility** do **SOLID**, no qual cada modelo possui unica e exclusiva a responsabilidade realizar as operações dos dados em nivel de entidade, sem qualquer ação com o banco de dados.

***OBS: Cada modelo da aplicação possui os seus testes unitários.***

<BR>

### Repositories
---
Nessa camada estão as classes responsáveis por realizar as interações com o banco de dados. Em cada classe foi utilizado o **Design Pattern Repository**, que permite, através da utilização de **interfaces**, o desacoplamento da aplicação com o banco de dados, assim encapsulando a logica de acesso de dados e facilitando os testes unitário da camada de UseCase.

Nesse projeto houveram duas classes de implentações sendo elas:

* **InMemoryUsersRepository:** Classe que simula uma persistencia com o banco de dados, sendo utilizado em testes unitários na camada de UseCase.

* **MongoDBUsersRepository**: Classe que implementa do driver do banco de dados MongoDB, realização os metodos de ***create, find e update*** na base de dados.

<BR>

### Services
---
Nessa camada estão as classes responsaveis por utilizarem algum serviço externo, como por exemplo a API do ChatGPT. Em cada classe foi utilizado o **Abstração** da Orientação a Objeto, gerando o desacoplamento da aplicação com a impletação dos serviços externos, facilitando os testes unitários da camada de UseCase.

<BR>

### UseCases
---
Nessa camada estão as classes responsáveis por realizar as regras de negócio da aplicação. Nessas classes são realizadas o direcionamento das classes de modelo a executarem suas regras de dominio e as funcionalidades das classes da camada Repositories de persistir as informações no banco de dodos, assim chegando ao objetivo do UseCase.

Juntamente, foi aplicado o conceito de **Dependency Inversion Principle** do **SOLID**, no qual o UseCase está dependendo apenas das abstrações e não das implementações das classes das camadas **Repositories** e **Services**.

***OBS: Cada UseCase da aplicação possui os seus testes unitários.***

<BR>

### Controllers
---
Nessa camada estão as classes responsáveis por receber os dados de entrada da API, já validados, em seguida chamar o Caso de Uso desejado e após a execução realizar uma resposta a requisição em caso de sucesso do caso de Uso.

Vale lembra que nessa camada foi aplicado o Design Pattern de **Injeção de Dependencia**, através da classe **SendMessageFactory**, nela é realizado as instâncias das classes que implementam as interfaces.

***OBS:*** ***Cada Controller da aplicação possui os seus testes de Integração.***


<BR>

### Schemas
---
Nessa camada estão as classes responsáveis por estabelecer as validações nos dados informados API através de um requisição. Essas classes foram desenvolvidas para serem utilizadas pela camada **Middlewares**.

<BR>

### Erros
---
Nessa camada estão as classe de erros personalizados da aplicação. Essas classes são utilizada pelas classes da camada Models e Camada UseCase. No caso de alguma excessão, esse erros são lançados, com isso facilitando uma possível correção de bug e também retornando uma resposta a mensagem e o status correto do ocorrido, pois cada classes de erro retorna um tipo de HTTP Status Code. 

<BR>

### Middlewares
___
Nessa camada estão os métodos que serão utilizados nas rotas do **Express**. Esses metodos possuem funções diversas sendo:

* **handlerErrors:** Método responsável por receber as excessão geradas pela aplicação e retornar uma resposta para a requisição. Esse método obtém o HTTP Status Code e message informado pelo erro personalizado e retorna uma resposta da requisição. 

    Vale ressaltar que o **Express na versão 4** não realiza gerenciamento de erro de funções assincronas, então para que esse handler de erros funcione corretamente, foi necessário adicionar o pacote **express-async-errors**, fazendo com que o handler de erros capture as exceções geradas por funções assincronas.

* **handlerErrors:** Método responsável por aplicar as regras de validações da camada de **Schema**, nos dados de entrada da API.

<BR>

### Routes
---
Nessa camada está a classe que contém as rotas da aplicação, adicionando os middlewares necessários para cada rota e chamando a class Factory que irá instaciar o controller desejado.

<BR>

### Database
---
Nessa camada está o objeto responsável por obter os dados das variáveis de ambiente e gerar uma conexão com o banco de dados MongoDB.

<BR>

### App.ts
___
Esse arquivo possui a responsabilidade de criar a aplicação **Express** e adicionar os middlewares globais necessários para subir a aplicação.

<BR>

### Server.ts
---
Esse arquivo possui a responsabilidade de cria uma conexão com o banco de dados e subir a aplicação Express.

<BR>

### Teste Unitários e Teste de Integração
---
Nesse projeto foi utilizado as bibliotecas Jest e Supertest para realizar os testes. Todos os arquivos com a nomeclatura **.test.ts**, são responsáveis por realizar os testes unitários, já os arquivos com a nomeclatura **.spec.ts**, são responsáveis por realizar os teste de integração. 

<BR>

### Padronização de Estilo de Código
___
Para esse fim fora utilizados as seguintes ferramentas: 

* **ESlint**: Responsável por identificar padrões de código em desacordo com as regras pré-estabelecidas.
  
* **Prettier**: Responsável por formatar o código de acordo com essas regras.

<BR>

### Swagger
___
Para documentação da API foi utilizado a ferramenta **Swagger**, nele estão contido as URL da aplicação, as rotas e a explicação da utilização de cada rota.

Para acessar o Swagger basta acessar este [link](https://api-tira-duvida-chatgpt-kenlo.onrender.com/swagger)

<BR>

## Execução do projeto
___
Segue abaixo as maneiras de executar o projeto localmente.

<BR>

### Execução do projeto com Docker
---

<BR>

Para realizar a execução do projeto via docker execute os comandos abaixo:

```bash
# Instale as dependencias do projeto
npm i

# No arquivo docker-compose.yaml adicione a chave para autenticação com a OpenAI
# Para obter essa chave acesse o link: https://platform.openai.com/api-keys 
OPENAI_API_KEY= # Chave gerada pela plataforma OpenAI

# Crie os containers da aplicação (NodeJS / MongoDB)
npm run up

# Para remover os container
npm run down
```

**OBS: Não é necessário alterar o arquivo .env pois o arquivo docker-compose já possui as variaveis de ambiente.**

<BR>

### Execução do projeto sem Docker
---

<BR>

Para realizar a execução do projeto via docker execute os comandos abaixo:

```bash
# Instale as dependencias do projeto
npm i

# Crie o arquivo .env
cp .env.example .env

# Altere as variaveis de ambiente com os dados do Banco de dados MongoDB
MONGODB_DATABASE_NAME=xxxx # Nome do Database que será gerado no MongoDB
MONGODB_URL=mongodb://xxxx:yyyy # URL do Servidor do MongoDB
MONGODB_USERNAME=xxxxx # Username utilizado para autenticação com o servidor MongoDB
MONGODB_PASSWORD=xxxx # Password utilizado para autenticação com o servidor MongoDB
PORT=xxxx # Porta da aplicação Express

# Para obter essa chave acesse o link: https://platform.openai.com/api-keys 
OPENAI_API_KEY= # Chave gerada pela plataforma

# Para executar o projeto
npm run start:dev
```
<BR>

## Deploy da Aplicação
___
Para realizar o deploy da aplicação em ambiente de produção foram utilizados os seguintes serviços:

- [Github Actions](https://github.com/)
- [Render](https://dashboard.render.com/)
- [Railway](https://railway.app/)
- [CircleCI](https://app.circleci.com/)

<BR>


### Requisitos:
___

- Ter conta criada em todos os serviços citados acima.
- O codigo do projeto deve estar em um repositório do Github
  

<BR>

## Execução do Deploy
---
1. Acesse a plataforma Railway.
   img1
2. Clique no botão " + New Project".
    img2
3. Selecione a opção "Provision MongoDB", e aguarde até o serviço ser gerado.
    img3
4. Clique no serviço MongoDB
    img4
5. 


<BR>











