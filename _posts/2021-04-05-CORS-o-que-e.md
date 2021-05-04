---
layout: post
title:  "CORS, o que é e como lidar no back end de sua aplicação"
date:   2021-05-03 19:24:45 -0300
categories:  Nodejs Express API
---

![consolelog](https://res.cloudinary.com/practicaldev/image/fetch/s--VVoC_7gM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/jdk4zort0fmt2vcdjil0.png)<br/><br/>
CORS é a sigla de <strong>Cross Origin Resource Share.</strong> Toda vez que o navegador precisa fazer uma requisição do tipo `XMLHttpRequest` ou `Fetch API` à  uma URL externa, por padrão, eles devem permanecer a uma mesma origem. Isso significa que requisições precisam obedecer políticas de cross origin (ou seja, permanecer à mesma origem). É o chamado <strong>Same-Origin Policy</strong>, (ou política de mesma origem). Essa política determina que como um documento ou script proveniente de uma origem pode interar com documento ou script de outra origem. Isso evita que ocorram falhas de segurança e falsificações entre sites, como ataques <strong> CSRF  (cross-site request forgery) </strong><br/>
Então, basicamente tudo que difere do <strong>Protocolo</strong>, <strong>host</strong> ou <strong>porta</strong> são considerados origens diferentes.<br/>
Exemplo:<br/>
`http://localhost:3000` é diferente de `http://meuhost:3000` que é diferente de `https://localhost:3000` que por sua vez é diferente de `https://localhost:5000`
<br/>

### Entendendo os tipos de Requests CORS 
Existem dois tipos de requisições CORS, as <strong>simples</strong> e as <strong>preflights</strong> e é o browser que determina qual usar. As requisições <strong>simples</strong> obedecem alguns critérios, como por exemplo: <br/>
 - um dos métodos utilizados é  `GET`, `POST`ou `HEAD`
 - um header CORS safe-listed é utilizado
 - quando utilizando o header `Content-Type` nas especificações `application/x-www-form-urlencoded`, `multipart/form-data`, or `text/plain` <br/>

Já as requests <strong>Preflight</strong> utilizam o método <strong>OPTIONS</strong> caso o navegador verifique que as condições da request do tipo <strong>Simples</strong> não foram satisfeitas. Nesta request, a especificação exige que navegadores "pré-enviem" a requisição, solicitando os métodos suportados pelo servidor, utilizando um método de requisição HTTP `OPTIONS` e, após a aprovação, o servidor envia a requisição verdadeira com o método de requisição HTTP correto. A requisição <strong>Preflight</strong> configura os headers 
 - `Access-Control-Request-Method`: Qual o método que será utilizado
 - `Access-Control-Request-Headers`: Uma indicação dos headers que serão enviados com a request
 - `Origin`: A origem usual do script

<br/>
### No lado do servidor. 

O servidor ira incluir alguns headers `Access-Control-*` indicando o que será aceito ou não. Isso inclui:
 - `Access-Control-Allow-Origin`: Basicamente a origem aceita, se for especificado um `*`, qualquer origem será aceita **perigo**
 - `Access-Control-Allow-Methods`: Uma lista dos métodos HTTP, separados por virgulas
 - `Access-Control-Allow-Headers`: Uma lista de headers aceitos, separados por virgulas
 - `Access-Control-Max-Age`: A duração máxima entre uma requisição preflight e outra
<br/><br/>

### Adicionando CORS a uma aplicação Nodejs Express 
No nosso `server/index.js`, vamos adicionar headers CORS ao app
{% highlight javascript %}
const express = require("express");
const app = express();
const port = process.env.SERVER_PORT || 3001;

app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header(
    "Access-Control-Allow-Headers",
    "Origin, X-Requested-With, Content-Type, Accept"
  );
  next();
});

// API endpoint
app.get("/api/ping", (req, res) => {
  res.send({
    msg: "Hello, World"
  });
});

app.listen(port, () => debug(`Listening on port ${port}`));
{% endhighlight %}<br/>

Ao invés de especificarmos os headers manualmente, podemos utilizar o CORS Express middleware package
{% highlight javascript %}
npm install cors
{% endhighlight %}<br/>

E no nosso mesmo `server/index.js`
{% highlight javascript %}
const express = require("express");

// importando a lib cors
const cors = require("cors");

const app = express();
const port = process.env.SERVER_PORT || 3001;

//utlizando a lib
app.use(cors());

app.get("/api/ping", (req, res) => {
  res.send({
    msg: "Hello, World"
  });
});

app.listen(port, () => debug(`Listening on port ${port}`));
{% endhighlight %}<br/>

Lógicamente, o middleware pode ser configurado para aceitar apenas algumas origens, métodos e tempo, como vimos acima. Por exemplo:
{% highlight javascript %}
app.use(
  cors({
    origin: "http://localhost:3000", // restringe as chamadas para esse endereço
    methods: "GET" //apenas requisições GET
  })
);
{% endhighlight %}<br/>

Ao invés de utilizarmos o servidor, também podemos utilizar extensões de navegadores como <strong>Allow CORS</strong> ou <strong>CORS unblock</strong>. <br/>

É importante salientar que essas soluções são eficazes na fase de testes e desenvolvimento, e o essencial é que se use o proxy de um web server como o <strong>Nginx</strong> por exemplo.