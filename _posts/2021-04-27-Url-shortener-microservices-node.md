---
layout: post
title:  "URL-Shortener microservice com NodeJS e Express"
date:   2021-04-27 19:24:45 -0300
categories:  Nodejs Express API Microservices
---

### Configurando o BD e criando nosso model

Primeiro, vamos adicionar `Mongoose` às dependências do nosso projeto e configurá-lo. Criei a pasta `db` dentro da pasta `src`

{% highlight javascript %}
//mongoose.js
const mongoose = require('mongoose')
const connectionURL = 'mongodb+srv://<user>:<user_password>@url-shortner.qidat.mongodb.net/myFirstDatabase?retryWrites=true&w=majority'

mongoose.connect(connectionURL, {
    useNewUrlParser: true,
    useCreateIndex: true,
    useUnifiedTopology: true,
    useFindAndModify: false
})
{% endhighlight %}

Aqui estou usando a versão cloud do MongoDB<br/>

Agora, criamos a pasta `models` dentro de `src`e adicionamos o arquivo `url.js`. Adicionamos as dependencias `validator` (para validar nossa entrada) e  `shortid` (para gerarmos uma URL encurtada única) ao nosso projeto

{% highlight javascript %}
//url.js
const mongoose = require('mongoose')
const validator = require('validator')
const shortId = require('shortid')


//basicamente iremos criar um model com campos url e shortnedUrl
const Url = mongoose.model('Url', {
    url:{
        type: String,
        required: true,
        trim: true,
        validate(value){
            if(!validator.isURL(value)){
                throw new Error('URL is invalid')
            }
        }
    },
    shortnedUrl:{
        type: String,
        default: shortId.generate
    }
})

module.exports = Url
{% endhighlight %} <br/>

O resto será feito diretamente no nosso servidor.

### Configurando nosso servidor

{% highlight javascript %}
const express = require('express')
const Url = require('./models/url')
//conectando com nosso DB
require('./db/mongoose')
const app = express()
const port = process.env.port || 3000

app.use(express.json())
app.use(express.urlencoded({ extended: true}))


//recebe uma url e armazena a URL e um shortID correspondente à ela na nossa DB
//retorna um objeto com a url original e o shortID referente à ela
app.post('/api/shorturl', async (req, res)=>{
    
    const url = await Url(req.body)
    try {
        await url.save()
        if(!url){
            return res.send({ error: 'unable to save' })
        }
        res.status(201).send({"original_url ": req.body.url, "short_url": url.shortnedUrl})
    } catch (e) {
        res.status(400).send({ 'error': 'invalid url' })
    }
})


//recebe o shortID, verifica se ela está presente na DB 
//redireciona para a URL original cadastrada 
app.get('/api/shorturl/:shortUrl', async (req, res) => {
    const paramShortUrl = req.params.shortUrl
    try {
        const matched = await Url.findOne({shortnedUrl: paramShortUrl})
        if(!matched){
            return res.status(404).send({"message" : "shortened url not found"})
        }
        res.redirect(matched.url)
    } catch (e) {
        
    }
})

//connecting server
app.listen(port, ()=>{
    console.log(`server connected on port ${port}`)
})
{% endhighlight %}<br/><br/>

E é isso, o core do projeto é esse, ele pode ser manipulado de diversas formas. Podemos criar um REGEX para validarmos a entrada do usuário (entradas do tipo "http://" ou "https://"), também podemos utilizar o `dns.lookup` do módulo padrão `dns`do Node para validarmos a URL.
Podemos também integrar facilmente uma interface, basta criarmos uma view html (ou qualquer outro tipo) e criarmos um arquivo Javascript para lidarmos com as requisições.
