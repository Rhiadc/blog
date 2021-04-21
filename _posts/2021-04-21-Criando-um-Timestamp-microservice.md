---
layout: post
title:  "Criando um Microserviço Timestamp com NodeJS"
date:   2021-04-21 19:24:45 -0300
categories:  Nodejs Express API Microservices
---

Uma requisição a `api/:date` com uma data válida retorna um objeto JSON (nosso timestamp) com um key `unix` contendo um timestamp em milisegundos. <br/>

O timestamp também retorna uma key `utc` contendo um valor no formato `Thu, 01 Jan 1970 00:00:00 GMT`<br/>

O parametro `/:date` aceita uma data `unix` em milisegundos, o timestamp permanece com as mesmas especificações<br/>

Caso o parametro `/:date` passado seja inválido, o objeto JSON deve ter a estrutura `{ error: 'Invalid Date' }`<br/>

Um parametro `/:date` vazio deve retornar uma timestamp com a data atual<br/>

Uma requisição para  `/api/1451001600000` deverá retornar `{ unix: 1451001600000, utc: "Fri, 25 Dec 2015 00:00:00 GMT" }`<br/>

Segue o código:

{% highlight javascript %}

app.get('/', (req,res)=>{
    res.send(
    'use /api/your_date to get a timestamp, please use a month-day-year format'
    )
})

//a função dateToUtc faz o handle do parametro date e retorna um objeto pronto
const dateToUtc = date =>{
    if(date !== ''){
        date = (parseInt(date) <= 12 && date.includes('-') ) ? date :
         parseInt(date)
    }
    const objDate = {
      unix: date==='' ? Date.now() : Date.parse(new Date(date)),
      utc: date==='' ? new Date().toUTCString() : new Date(date).toUTCString()
    }
    return objDate
}

app.get('/api/:date?', (req,res) => {
    /*o primeiros verificamos se o parametro date foi definido, caso não, 
    invocamos a função dateToUtc com uma string vazia como parametro.*/
    objDate = !req.params.date ? dateToUtc('') : dateToUtc(req.params.date)

    /*caso a resposta a data seja inválida, o objeto gerado pela função conterá 
    o valor 'Invalid Date', caso a key utc contenha esse valor, 
    geramos um objeto de erro, caso não, retornamos o objeto original*/

    objDate.utc === 'Invalid Date' ? 
        res.send({error: objDate.utc}) : res.send(objDate)
    
})

{% endhighlight %}
