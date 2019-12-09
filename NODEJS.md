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

### Sucrase e Nodemon

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
