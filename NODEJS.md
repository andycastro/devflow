Este documento auxilia na criação de uma REST API utilizando NodeJs e Postgres

**Contents**

- [Iniciando aplicação NodeJS](#iniciando-aplica%c3%a7%c3%a3o-nodejs)
  - [Configurações iniciais](#configura%c3%a7%c3%b5es-iniciais)
  - [Sucrase e Nodemon](#sucrase-e-nodemon)
  - [Configurando o Sequelize](#configurando-o-sequelize)
  - [Model de usuários](#model-de-usu%c3%a1rios)
  - [Criando loader de models](#criando-loader-de-models)
  - [Cadastro de usuários](#cadastro-de-usu%c3%a1rios)
  - [Gerando hash de senhas de usuários](#gerando-hash-de-senhas-de-usu%c3%a1rios)
  - [Autenticação de usuários com JWT - Json Web Token](#autentica%c3%a7%c3%a3o-de-usu%c3%a1rios-com-jwt---json-web-token)
  - [Middware de autenticação](#middware-de-autentica%c3%a7%c3%a3o)
  - [Update do usuário](#update-do-usu%c3%a1rio)

# Iniciando aplicação NodeJS

## Configurações iniciais

Inicialmente iremos criar o nosso arquivo package.json utilizando o comando abaixo:

``` yarn init -y ```

Feito isso, iremos instalar o Express, que é um framework para Node.js que nos permite ter um conjunto de recursos a nossa aplicação.

``` yarn add express ```

Dessa forma, iremos iniciar a organização de nossos diretórios, para que a aplicação fique bem fluida.

No diretório raiz, vamos criar uma pasta chamada src, é nela que todo o código da nossa aplicação estará.

```mkdir src```

Vamos entrar na pasta src, e nela criaremos três arquivos para organizar a nossa aplicação, app.js, server.js e route.js.

```cd src; touch app.js; touch server.js; touch routes.js```

No arquivo app.js iremos configurar o nosso servidor adicionando o seguinte código:

```
//importa o express
const express = require('express'); 
//importa as rotas do arquivo routes.js
const routes = require('./routes');

//define a classe App
class App {
  constructor(){
    this.server = express();
    
    this.middwares(); //chama o método
    this.routes(); //chama o método
  }

  middlewares(){
    this.server.use(express.json());
  }
  routes() {
    this.server.use(routes);
  }
}

module.exports = new App().server;

```

Uma vez com estas configurações criadas, iremos abrir o arquivo server.js e importar o App, inserindo as seguintes linhas de código:

```
const app = require('./app');
app.listen(3333);
```

Agora vamos às rotas! Abra o arquivo routes.js para importar o Router do express. Digite o seguinte código:

```
const { Router } = require('express');

```
Assim, estamos importando apenas o carinha Router do express, ao invés de importar todo o express, que é uma forma da gente separar o roteamento em outro arquivo.

Seguindo, iremos colocar isso em uma variável, da seguinte forma:

```
const routes = new Router();
```
Agora iremos chamar uma rota, passando o req e res e retornando um json para testar se está tudo ok.

```
routes.get('/', (req, res) => {
  return res.json({ message: 'Hello World' });
})
```
E por fim, iremos exportar nossas rotas, deixando-as visíveis para o restante da aplicação:

```
module.exportes = routes;
```

Feito isso, está na hora de rodar o servidor para conferir se está tudo ok.

Execute o seguinte comando no terminal:

```
node src/server.js
```

Agora abra o browser e acesse localhost:3333.

Se tudo deu certo, o retorno deverá ser:

```
{
  "message": "Hello World!"
}
```

---

## Sucrase e Nodemon

Bom, agora que rodamos o servidor, iremos configurar duas ferramentas interessantes em nosso projeto.

Uma delas é o Sucrase, que nos permite utilizar algumas das novas funcionalidades do JavaScript,como por exemplo o import, ao invés do require.

A outra ferramenta é o Nodemon, que nos permite atualizar o servidor de forma automática a cada vez que salvamos nossos arquivos. Isso irá nos economizar tempo e bugs.

Então vamos lá. Vamos executar o comando com a flag -D, para dependências de desenvolvimento:

```yarn add sucrase nodemon -D```

Feito isso, basta alterar os requires por imports, ficando dessa forma:
```
import express from 'express'; //exemplo 

```
E também podemos substituir o ```module.exports``` por ```export default```

Para validar estas alterações, iremos configurar o nodemon para reconhecer o sucrase e automatizar o processo também.

Vamos abrir o package.json e adicionar configurações de scripts para facilitar este processo.

Iremos adicionar a seguinte linha ao arquivo:

```
"scripts": {
  "dev": "nodemon src/server.js"
}
```

Na sequência iremos criar um arquivo chamado nodemon.json na raiz do nosso projeto para fazer com que a nossa aplicação possa entender as funcionalidades utilizando o sucrase.

Então vamos criar o arquivo ```touch nodemon.json``` e vamos adicionar um objeto dentro dele com as seguintes configurações:
```
{
  "execMap": {
    "js": "node -r sucrase/register"
  }
}
```
Com este código estamos solicitando a execução do sucrase para depois rodar os demais scripts.

Agora sim, se você rodar ```yarn dev``` irá iniciar o servidor e ficar escutando as alterações de forma automática.

Vamos apenas configurar o debug para rodar normalmente com nodemon.

No package.json iremos adicionar o seguinte script dentro de scripts:

```
"dev:debug": "nodemon --inspect src/server.js"
```
Ficando dessa forma:
```
"scripts": {
  "dev": "nodemon src/server.js",
  "dev:debug": "nodemon --inspect src/server.js"
}
```
Clicando na aba do bug no VsCode iremos adicionar uma nova configuração:
```
{
  "name": "Launch Program",
  "restart": true,
  "protocol": "inspector"
}
```
## Configurando Docker

Após o download e instalação do Docker [Download aqui!](https://docs.docker.com/docker-for-mac/install/), iremos utilizar o terminal para configurar a nossa aplicação com Docker.

Primeiramente, iremos adicionar o serviço para o banco de dados que iremos utilizar, neste caso, postgres.

No terminal execute:
```
docker run --name databaseName -e POSTGRES_PASSWORD=qualquersenha -p 5432:5432 -d postgres
```

Na sequência, iremos executar ```docker ps``` para ver as imagens em execução.

Agora vamos baixar um programa chamado de POSTBIRD, para nos auxiliar na visualização do nosso banco de dados, e iremos entrar com os dados de aceso ao banco que criamos.

[Faça o download aqui do PostBird](https://electronjs.org/apps/postbird)

Todo o trabalho de manipulação de banco de dados será feito pela aplicação e iremos utilizar o postbird apenas para consulta, se for preciso, mas precisaremos deixar nosso BD rodando.

## Sequelize (ORM) e MVC (Model, Views e Controllers)

O Sequelize irá nos auxiliar na manipulação de dados no banco de dados.

**Migrations** São controle de versões para base de dados, no qual cada arquivo contém instruções para criação, edição ou remoção de tabelas e colunas.

As migrations mantém a base de dados atualizada entre todos os desenvolvedores do time e também no ambiente de produção sendo cada arquivo uma migration ordenada por data.

Dois métodos são utilizados nas migrations, o **UP** para executar a migration e o **DOWN** para realizar um rollback.

Abaixo um exemplo de migration do sequelize para criar uma nova tabela no banco de dados:

```
module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('users', {
      id: {
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
        type: Sequelize.INTEGER
      },
      name: {
        allowNull: false,
        type: Sequelize.STRING
      },
      email: {
        allowNull: false,
        unique: true,
        type: Sequelize.STRING
      }
    })
  },

  down: (queryInterface, Sequelize) => {
    return queryInterface.dropTable('users')
  }
}
```
**SEEDS** Nos auxiliam na população de banco de dados no ambiente de desenvolvimento, criando usuários, produtos e outros campos que precisarmos para testar a aplicação.

Os SEEDS não serão utilizados em produção, somente em desenvolvimento.

**Arquitetura MVC - Model, View e Controller** 

Model: armazena a abstração do banco de dados, utilizado para manipular os dados contidos nas tabelas do banco.

Controller: É o ponto de entrada das requisições da nossa aplicação, no qual uma rota geralmente estará associada diretamente com um método do controller. Podemos incluir grande parte das regras de negócios da aplicação no controller.
- São basicamente um classe
- Sempre retornam um JSON
- Terão 5 métodos (não mais que isso)
  - index() {}    //lista usuários
  - show() {}     //exibe um único usuário
  - store() {}    //cadastra usuário
  - update() {}   //altera usuário
  - delete() {}   //remove usuário

View: É o retorno do cliente, ou seja, o que chega ao final da aplicação para entregar o conteúdo ao cliente.

## Eslint, Prettier e EditorConfig

Agora iremos configurar algumas ferramentas de desenvolvimento para padronizar nosso código e trabalhar de forma padronizada em equipe.

Iremos instalar e configurar Eslint, Prettier e o EditorConfig.

Instalando e configurando Eslint:

```yarn add eslint -D```

Após instalar, iremos rodar o arquivo de configuração, iniciando o Eslint ```yarn eslint --init``` e iremos avançar com as seguintes opções:
- To check syntax, find problems, and enforce code style
- JavaScript modules (import/export)
- None of these (pergunta se usa React ou Vue.js)
- Node (barra de espaço para selecionar)
- Use a popular style guide
- Airbnb
- JavaScript
- Would you like to install them now with npm? Y
  
Por padrão será utilizado o npm ao instalar as dependências que configuramos, mas como vamos utilizar yarn, iremos excluir o arquivo que foi criado (package-lock.json) e iremos rodar ```yarn``` na pasta do nosso projeto para criar o arquivo **.eslintrc.js**, onde iremos setar as nossas configurações.

Verifique se no seu VSCode já está com ESLint instalado, se não, [clique aqui para instalar.](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

Vamos abrir o arquivo de configuração do VSCode settings.json executando o comando (command + p).

As configurações para o ESLint são:
```
//Elint preferences
"eslint.autoFixOnSave": true,
"eslint.validate": [
  {
    "language": "javascript",
    "autoFix": true
  },
  {
    "language": "javascriptreact",
    "autoFix": true
  }
  
],
//
```

No qual ```"eslint.autoFixOnSave": true,``` fará as correções automaticamente e ```"eslint.validate"``` nos permite aplicar para JavaScript e JavaScript React.

Agora iremos sobrescrever algumas regras, editando o arquivo **.eslintrc.js**.

```
rules: {
  "class-methods-use-this": "off",
  "no-param-reassign":"off",
  "camelcase":"off",
  "no-unused-vars": ["error", { "argsIgnorePattern": "next" }],
}
```

Feito isso, iremos instalar o Prettier, para nos auxiliar no "design" do nosso código.

Então, iremos executar no terminal o seguinte comando:

```yarn add prettier eslint-config-prettier eslint-plugin-prettier -D```

Então no **.eslintrc.js** iremos adicionar o seguinte código:

```
extends: ['airbnb-base', 'prettier'],
plugins: ['prettier'],
rules: {
  "prettier/prettier": "error"
}
```

Ok, precisamos agora criar um arquivo na raiz do nosso projeto chamado **.prettierrc** para ajustar o comportamento do Prettier com ESLint.

Nele, adicionaremos o seguinte código:

```
{
  "singleQote": true,
  "trailingComma": "es5"
}
```
Para corrigir todos os arquivos de forma automática, iremos executar no terminal o seguinte comando:

```yarn eslint --fix src --ext .js```

Por final, iremos instalar uma extensão em nosso VSCode chamado **editorconfig** [Download aqui.](https://marketplace.visualstudio.com/items?itemName=EditorConfig.EditorConfig)
e iremos, na raiz do projeto, dentro do VSCode, clicaremos com botão direito e **Generate .editorconfig** para gerar o arquivo de configuração para padronizar algumas regras para editores de texto.

Editando o arquivo gerado anteriormente, iremos passar o parâmetro **true** para as seguintes opções:

```
trim_trailing_whitespace = true
insert_final_newline=true
```

Dessa forma, caso outros programadores utilizem editores diferentes, isso garantirá um maior padrão para nosso código.

## Configurando o Sequelize

Dentro da pasta **src** iremos criar um conjunto de novas pastas e alguns arquivos:
- config
  - database.js //conterá nossas configurações de acesso ao banco de dados
- database
  - migrations
- app
  - controllers
  - models

Iremos instalar o **Sequelize** e **Sequelize Cli**

```yarn add sequelize``` e ```yarn sequelize-cli -D```

Criaremos o arquivo **.sequelizerc** em nossa raiz para configuração das nossas migrations.

Editando o **.sequelizerc** temos o seguinte:

```
const { resolve } = require('path');

module.exports = {
  config: resolve(__dirname, 'src', 'config', 'database.js'),
  'models-path': resolve(__dirname, 'src', 'app', 'models'),
  'migrations-path': resolve(__dirname, 'src', 'database', 'migrations'),
  'seeders-path': resolve(__dirname, 'src', 'database', 'seeds'),
};
```

Feito isso, iremos editar o arquivo **src/config/databse.js** para conexão com banco de dados:

```
module.exports = {
  dialect: 'postgress',
  host: 'localhost',
  username: 'USER_NAME',
  password: 'PASSWORD',
  database: 'DATABASE_NAME',
  define: {
    timestamps: true,
    underscored: true,
    underscoredAll: true,
  },
}
```

Para utilizar o dialect Postgress, iremos executar o comando ```yarn add og pg-hstore``` em nosso terminal.

E por aqui finalizamos a nossa configuração do Sequelize.

## Migration de usuário

Para criar a nossa migration com auxílio do Sequelize, iremos executar o seguinte comando no terminal:
```yarn sequelize migration:create --name=create-users```, onde **create-users** é o nome que estamos utilizando neste momento, mas poderia ser **create-products** e por ai vai.

Após isso, podemos verificar que dentro da pasta **migrations** foi gerado um arquivo e é nele que iremos passar as informações dos campos que queremos criar. Vamos editá-lo para a nossa finalidade. Abaixo, um arquivo de exemplo:

```
'use strict'

module.exports = {
  up: (queryInterface, Sequelize) => {
    return queryInterface.createTable('users', {
      id: {
        type: Sequelize.INTEGER,
        allowNull: false,
        autoIncrement: true,
        primaryKey: true,
      },
      name: {
        type: Sequelize.STRING,
        allowNull: false,
        unique: true,
      },
      password_hash: {
        type: Sequelize.STRING,
        allowNull: false,
      },
      provider: {
        type: Sequelize.BOOLEAN,
        defaultValue: false,
        allowNull: false,
      },
      created_at: {
        type: Sequelize.DATE,
        allowNull: false,
      },
      updated_at: {
        type: Sequelize.DATE,
        allowNull: false,
      }
    });
  },

  down: queryInterface => {
    return queryInterface.dropTable('users');
  } 
}
```

Vamos executar a nossa migration utilizando o comando ```yarn sequelize db:migrate``` e conferir no PostBird o resultado da criação dos nossos campos no banco de dados.

Para desfazer uma migration, caso precise, basta rodar o comando ```yarn sequelize db:migrate:undo``` para a última alteração e ```yarn sequelize db:migrate:undo:all``` para todas as migrations.

## Model de usuários

Iremos criar agora os models de usuários, para poder criar, editar e excluir usuários.

Vamos criar um arquivo dentro de **models** chamado **User.js**.

```
import Sequelize { Model } from 'sequelize';

class User extends Model {
  static init(sequelize) {
    super.init(
      {
        name: Sequelize.STRING,
        email: Sequelize.STRING,
        password_hash: Sequelize.STRING,
        provider: Sequelize.BOOLEAN
      },
      {
        sequelize,
      }
    );
  }
}

export default User;
```

## Criando loader de models

Vamos criar um arquivo que irá fazer a conexão com banco de dados postgres.

Dentro da pasta **database** iremos criar um arquivo chamado **index.js** e nele conterá o seguinte script:

```
import Sequelize from 'sequelize';

import User from '../app/models/User';

import databaseConfig from '../config/database';

const models = [User];

class Database {
  constructor(){
    this.init();
  }

  init(){
    this.connection = new Sequelize(databaseConfig);

    models.map(model => model.init(this.connection));
  }
}

export default new Database();
```

Feito isso, iremos importar este nosso arquivo de conexão com banco de dados no arquivo **app.js**, inserindo um ```import './database'```.

## Cadastro de usuários

Vamos iniciar criando nosso controller de usuários chamado de **UserController.js** para criação de usuários.

O arquivo final do **UserController.js** ficará neste formato:

```
import User from '../models.User';

class UserController {
  async store(req, res){
  const userExists = await User.findOne({ where: { email: req.body.email } });

  if(userExists){
    return res.status(400).json({ error: 'User already exists.' });
  }

    const { id, name, email, provider } = await User.create(req.body);

    return res.json({
      id,
      name,
      email,
      provider,
    });

  }
}

export default new UserController();
```

Vamos precisar editar nosso arquivo routes.js e o final ficará dessa forma:

```
import { Router } from 'express';

import UserController from './app/controllers/UserController';

const routes = new Router();

routes.post('/users', UserController.store);

export default routes;
```

Para testar, iremos abrir o Insomnia e criar um novo workspace **nomeDoProjeto**, criar uma pasta Users, configurar a base_url nos enviroments.

Dentro da pasta Users iremos criar um POST "Create" com os seguintes dados e executar:

```
{
  "name": "Nome do Usuário",
  "email": "email@email.com",
  "password_hash": "123456"
}
```

## Gerando hash de senhas de usuários

Neste módulo iremos utilizar o **bcryptjs**, dessa forma iremos adicionar ele utilizando ```yarn add bcryptjs```.

Acessando **models/User.js** iremos importar o **bcryptjs**, então: ```import bcrypt from 'bcryptjs'``` e iremos adicionar um novo campo chamado **password**. ```password: Sequelize.VIRTUAL```. O **VIRTUAL** indica que ele não estará presente no banco de dados, apenas de forma virtual na aplicação.

Após o **super.init** iremos utilizar uma funcionalidade de hook do Sequelize ```this.addHook('beforeSave', () => {});```. O beforeSave executará antes de qualquer ação de criação de usuários, sendo assim, poderemos manipular estes dados anteriormente ao salvamento no banco de dados.

Então, iremos fazer uma verificação, antes do cadastro do usuário, desta forma:

```
this.addHook('beforeSave', async (user) => {
  if (user.password) {
    user.password_hash = await bcrypt.hash(user.password, 8);
  }
});
```
O que está acontecendo aqui? Basicamente o usuário informará a sua senha **password**, que é apenas **VIRTUAL**, e com **beforeSave** estamos pegando essa password e cryptografando com o **bcryptjs** com round/força 8 de cryptografia.

O nosso arquivo final do **models/User.js** ficará assim:

```
import Sequelize, { Model } from 'sequelize';
import bcrypt from 'bcryptjs';

class User extends Model {
  static init(sequelize) {
    super.init(
      {
        name: Sequelize.STRING,
        email: Sequelize.STRING,
        password: Sequelize.VIRTUAL,
        password_hash: Sequelize.STRING,
        provider: Sequelize.BOOLEAN,
      },
      {
        sequelize,
      }
    );
    
    this.addHook('beforeSave', async (user) => {
      if (user.password) {
        user.password_hash = await bcrypt.hash(user.password, 8);
      }
    });

    return this;
  }
}

export default User;

```
**Testando** no Insomnia, cadastra um novo usuário e confere no Post Bird.

## Autenticação de usuários com JWT - Json Web Token

A partir daqui, iremos iniciar o processo de autenticação dos usuários da nossa 
aplicação utilizando o JWT.

Na pasta **controllers** iremos criar um arquivo chamado **SessionController.js** e nele iremos importar o models **User** ```import User from '../models/User';``` e iniciar a nossa classe com **async** para podermos utilizar o **await** e o método **store** para criação da sessão, dessa forma: 
```
class SessionController {
  async store(req, res) {

  }
}
```
Por final, exportaremos ```export default new SessionController();```.

O nosso arquivo inicial SessionController.js ficará assim:

```
import User from '../models/User';

class SessionController {
  async store(req, res){

  }
}

export default new SessionController();
```
Iremos também instalar o **jsonwebtoken** executando o seguinte comando no terminal: ```yarn add jsonwebtoken```. Ele será responsável por gerar o nosso token.

Finalizada a instalação, iremos importar ele no **SessionController.js** 

```
import jwt from 'jsonwebtoken';
```

No corpo da nossa classe iremos pegar o email e senha do usuário 

```
const { email, password } = req.body
``` 

e iremos fazer uma verificação para validar o usuário 

```
const user = await User.findOne({ where: {email} });
``` 

e se esse usuário não existir iremos criar uma condição para barrar este acesso apresentando uma mensagem **User not found** dessa forma: 

```
if (!user) {
  return res.status(401).json({ error: 'User not found' });
}
```
E também iremos verificar a senha do usuário, criando um método (após o **return this; }**) no arquivo User.js, dessa forma: 
```
checkPassword(password) {
  return bcrypt.compare(password, this.password_hash);
}
``` 
O nosso arquivo final do **models/User.js** ficará assim:

```
import Sequelize, { Model } from 'sequelize';
import bcrypt from 'bcryptjs';

class User extends Model {
  static init(sequelize) {
    super.init(
      {
        name: Sequelize.STRING,
        email: Sequelize.STRING,
        password: Sequelize.VIRTUAL,
        password_hash: Sequelize.STRING,
        provider: Sequelize.BOOLEAN,
      },
      {
        sequelize,
      }
    );
    
    this.addHook('beforeSave', async (user) => {
      if (user.password) {
        user.password_hash = await bcrypt.hash(user.password, 8);
      }
    });

    return this;
  }

  checkPassword(password) {
    return bcrypt.compare(password, this.password_hash);
  }
}

export default User;

```

Voltando ao nosso **SessionController.js** iremos verificar o password do usuário, verificando se a senha não bate. Segue:

```
if (!(await user.checkPassword(password))) {
  return res.status(401).json({ error: 'Password do not match!' })
}
```
Então, se passar pelas validações que colocamos até agora em nossa aplicação o usuário seguirá o fluxo. Vamos prosseguir armazenando o **id** e **name** do usuário e iremos retornar os dados do usuário como também o **token**, urilizando o método **sign**, desta forma:

```
const { id, name } = user;

return res.json({
  user: {
    id,
    name,
    email
  },
  token: jwt.sign({ id }, 'hashaleatoria', {
    expiresIn: '7d',
  }),
})
```

Verificando de perto a variável **token** nós temos o **jwt.sign** recebendo o **id** do usuário e no segundo parâmetro uma **hash** gerada aleatoriamente [pode ser gerada no MD5 Online clicando aqui](https://www.md5online.org/) e temos também um terceiro parâmetro **expiresIn** que configura 7 dias para expiração do token ```expiresIn: '7d'```.

Agora que entendemos isso, iremos chamar o nosso arquivo de token em nossa rota (**routes.js**) importando nosso arquivo ```import SessionController from './app/controllers/SessionController';``` e ```routes.post('./sessions', SessionController.store);```.

Tá na hora de testar, então iremos abrir o **Insomnia**, criar uma pasta chamada **Sessions** e nela iremos criar uma rota **POST** com url **sessions** e iremos passar **email** e **password** dessa forma:
```
{
  "email": "nome@usuario.com.br",
  "password": "123456"
}
```
Após clicar em **Send** o resultado deverá ser parecido com este:
```
{
  "user": {
    "id": 4,
    "name": "name usuario",
    "email": "email@usuario.com.br"
  },
  "token": "tokenGerado"
}
```
Se tá parecido, ótimo, tudo funcionando.

Vamos só organizar o nosso arquivo **SessionController.js** criando um arquivo **auth** para organizar a parte de autenticação da nossa aplicação.

Dentro de config iremos criar um arquivo chamado **auth.js** e nele teremos:

```
export default {
  secret: 'hashaleatoria', //recebe a hash gerada no md5 online
  expiresIn: '7d'
}
```

Iremos importar nosso arquivo **hash.js** no **SessionController.js** ```import auth from '../../config/auth';``` e iremos substituir os valores de **hash** e **expiresIn** ficando assim:

```
token: jwt.sign({ id }, 'authConfig.secret', {
    expiresIn: authConfig.expiresIn,
  }),
```

## Middware de autenticação

Agora que nosso projeto está tomando forma, precisamos criar algumas limitações para que tipos de usuários possam ou não acessar alguma funcionalidade ou página específica e para isso iremos utilizar os middwares.

Este próximo middlware que iremos criar, irá verificar se o usuário está logado na aplicação, recebendo o token via Header/bearer.

Então vamos no **Insomnia** e criar uma requisição com método **put** em **Users** chamada **Update** e nela iremos passar apenas **email** e **password** como forma de edição de dados de usuários, mas ou menos dessa forma:

```
{
  "email": "email@usuario.com",
  "password": "123456"
}
```
Após isso, iremos no **UserController.js** criar um método de **update** logo após o método **store**:

```
async update(req, res) {
  return res.json({ ok: true });
}
```
Iremos adicionar a rota também em **routes.js**

```
routes.put('/users', UserController.update);
```

**Enviando Tokens** - Utilizando o Insomnia, iremos enviar os tokens ou via **Header**, informando **Authorization** e **Bearer token** ou em **Auth**, selecionando **Bearer Token** e informando o **token**.

Vamos criar uma pasta dentro de **app** chamada **middlewares** e dentro dela iremos criar um arquivo chamado **auth.js** que conterá a nossa função para verificar se o usuário está logado.

```
export default (req, res, next) => {
  const authHeader = req.headers.authorization;
  
  console.log(authHeader);
  return next();
}
```

e iremos importar nosso middleware na rota e definiremos ele globalmente, e tudo que estiver abaixo dele será impactado, veja como ficará nosso arquivo de rotas:

```
///routes.js

import { Router } from 'express';

import UserController from './app/controlllers/UserController';
import SessionController from './app/controllers/SessionController';

import authMiddleware from './app/middlewares/auth';

const routes = new Router();

routes.post('/users', UserController.store);
routes.post('/sessions', SessionController.store);

routes.use(authMiddleware); //middleware global afetando rotas abaixo dele

routes.put('/users', UserController.update);

export default routes;
```

**Testando** Ao enviar pelo Insomnia uma alteração já cai no console.log o token.

Continuando, vamos validar a autenticação do usuário quando ele for utilizar a rota de update e nosso arquivo **middlewares/auth.js** ficará assim:

```
import jwt from 'jsonwebtoken';
import { promisify } from 'util';
import authConfig from '../../config/auth';

export default async (req, res, next) => {
  const authHeader = req.headers.authorization;
  
  if (!authHeader) {
    return res.status(401).json({ error: 'Token not provided' })
  }

  const [, token] = authHeader.split(' ');

  try {
    const decoded = await promisify(jwt.verify)(token, authConfig.secret);

    req.userId = decoded.id;
  }catch (err) {
    return res.status(401).json({ error: 'Invalid Token' });
  }

  return next();
}
```

## Update do usuário

Iremos fazer o update dos dados do usuário e primeiramente iremos padronizar para que quando o usuário for fazer o update da senha ele tenha que digitar a senha antiga e a nova. Em nosso Insomnia a estrutura será parecida com essa:

```
{
  "name": "Nome Usuário",
  "email": "usuario@email.com.br",
  "oldPassword": "123456",
  "password": "12345678"
}
```






Arquivo final de **UserController.js** com validação e update do usuário:

```
import * as Yup from 'yup';
import User from '../models/User';

class UserController {
  async store(req, res) {
    const schema = Yup.object().shape({
      name: Yup.string().required(),
      email: Yup.string()
        .email()
        .required(),
      password: Yup.string()
        .required()
        .min(6),
    });

    if (!(await schema.isValid(req.body))) {
      return res.status(400).json({ error: 'Validation fails' });
    }

    const userExists = await User.findOne({ where: { email: req.body.email } });

    if (userExists) {
      return res.status(400).json({ error: 'User already exists' });
    }

    const { id, name, email, provider } = await User.create(req.body);

    return res.json({
      id,
      name,
      email,
      provider,
    });
  }

  async update(req, res) {
    const schema = Yup.object().shape({
      name: Yup.string(),
      email: Yup.string().email(),
      oldPassword: Yup.string().min(6),
      password: Yup.string()
        .min(6)
        .when('oldPassword', (oldPassword, field) =>
          oldPassword ? field.required() : field
        ),
      confirmPassword: Yup.string().when('password', (password, field) =>
        password ? field.required().oneOf([Yup.ref('password')]) : field
      ),
    });

    if (!(await schema.isValid(req.body))) {
      return res.status(400).json({ error: 'Validations fails' });
    }

    const { email, oldPassword } = req.body;

    const user = await User.findByPk(req.userId);

    if (email !== user.email) {
      const userExists = await User.findOne({ where: { email } });

      if (userExists) {
        return res.status(400).json({ error: 'User email already exists' });
      }
    }

    if (oldPassword && !(await user.checkPassword(oldPassword))) {
      return res.status(401).json({ error: 'Password does not match' });
    }

    const { id, name, provider } = await user.update(req.body);

    return res.json({
      id,
      name,
      email,
      provider,
    });
  }
}

export default new UserController();

```