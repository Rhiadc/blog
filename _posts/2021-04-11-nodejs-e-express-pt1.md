---
layout: post
title:  "Criando APIs com Node e Express. pt. 01"
date:   2021-04-11 23:24:45 -0300
categories: Nodejs Express Javascript 
---

`Node.js` é um ambiente de execução Javascript server-side, já o `Express` é um framework para aplicações web do Node.js<br/><br/>

### Criando um servidor express 


{% highlight javascript %}
//server.js

//importando o express
const express = require('express')
//criando nova instância do express
cons app = express()
//port que sera usada no servidor
const port = 3000

app.get('/', (req, res)=>{
    res.send("Hello world")
})
{% endhighlight %}<br/>

### Basic Routing 
`Routing`se refere à como uma aplicação responde à uma requisição do client, com uma `URI` ou verbo HTTP
{% highlight javascript %}

//estrutura básica da route
app.METHOD(PATH,HANDLER)

{% endhighlight %}<br/>

`app` é a instância express <br/>
`METHOD` são os verbos HTTP, sendo eles `get`, `post`, `put`, `post` etc... <br/>
`PATH` é o caminho para o endpoint do servidor, aceita regex e parâmetros, por exemplo: `'/users/:userID/book/:bookID` <br/>
`HANDLER` é a funçao callback, podendo ser esta com ou sem middleware, veja o exemplo:
{% highlight javascript %}

(req, res) => {
    res.send('sou a função callback)
}

(req, res, next) => {
    console.log('a resposta sera dada...')
    next()
}(req, res) => {
    res.send("... agora!')
}
{% endhighlight %}<br/>

### Objeto de resposta (res) 
Tem o objetivo de mandar uma mensagem ao client e terminar o request/response cycle, algum método do objeto response precisa ser chamado para terminar o ciclo. Alguns exemplos de métodos: <br />
`res.download()` Solicita que seja efetuado o download de um arquivo.
`res.end()` Termina o processo de resposta.<br />
`res.send()` Envia uma resposta de vários tipos <br/>
etc...<br /><br />

### app.route
Cria uma chain de routes, exemplo
{% highlight javascript %}
app.route('/book)
    .get((req,res)=>{
        res.send('get a book')})
    .post((req,res)=>{
        res.send('add a book')})
    .put((req,res)=>{
        res.send('update a book')})  
{% endhighlight %}<br/>  

### usando o express.Router()
Agrupa manipuladores de rotas (as vezes é necessário acessa-las utilizando um prefixo), podem conter middlewares também.
{% highlight javascript %}
//surf.js

var express = require('express')
var router = express.Router()

//middleware especifico para esse router
router.use(timeLog = (req, res, next) =>{
    console.log('time: ', Date.now())
    next()
})

router.get('/about', (req, res)=>{
    res.send('about surf')
})

//exportando o módulo
module.exports = router;
{% endhighlight %}<br/>  

Em seguida, carregue o módulo roteador no aplicativo:
<br/>

{% highlight javascript %}

var surf = require('./surf');
...
app.use('/surf', surf);
{% endhighlight %}<br/> 


