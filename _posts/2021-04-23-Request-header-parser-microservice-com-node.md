---
layout: post
title:  "Request header parser microservice com NodeJS e Express"
date:   2021-04-21 19:24:45 -0300
categories:  Nodejs Express API Microservices
---

Uma requisição para `/api/whoami` retorna um objeto JSON com uma key `ipaddress` com um valor do IP público do user. <br/>Uma key `language` com a linguagem principal, e a key `software` com as definições de browser e OS.<br/>

Segue o código:

{% highlight javascript %}

const express = require('express')

const app = express()

//usando a lib padrão http do Node
const http = require('http');
let ip  = ''

//pegando o IP público atraves de uma API pública
var options = {
  host: 'ipv4bot.whatismyipaddress.com',
  port: 80,
  path: '/'
};

http.get(options, res => {
      res.on("data", chunk => {
      ip = chunk.toString();
    });
  }).on('error', e => {
    console.log("error: " + e.message);
  });


// montando o objeto que será enviado manipulando o req.headers
const port = process.env.port || 3000
app.get('/api/whoami', (req,res)=>{
    let language = []
    language.push(req.headers['accept-language'].split(',')[0])
    language.push(req.headers['accept-language'].split(',')[1])
    res.send({
        ipaddress: ip, 
        language: language.join(','), 
        software: req.headers['user-agent'] 
    })
})

app.listen(port, ()=>{
    console.log(`server running on port ${port}`)
})

{% endhighlight %}