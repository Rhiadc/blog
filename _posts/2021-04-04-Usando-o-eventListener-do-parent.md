---
layout: post
title:  "Usando o eventListener do parent element para manipulação de uma child "
date:   2021-04-03 23:24:45 -0300
categories: jekyll update
---

Seja um exemplo simples de uma aplicação com diversos botões. podemos usar um addEventListener de um container parent desses botões para acessar a propriedade individual de cada um. Exemplo:
{% highlight javascript %}
buttonContainer.addEventListener('click', event=>{
    console.log("evento")
    if(event.target.nodeName === "BUTTON"){
        let buttonProperty  = event.target.getAttribute('someProperty') 
         doSomething(buttonProperty)
    }
})
{% endhighlight %}<br /><br />
No código acima, ao clicarmos num elemento do tipo `button`,  conseguimos acesso a sua propriedade. Neste caso, podemos realizar diversas operações com este elemento.
Esse esquema pode ser usado de maneira similar para qualquer elemento `child` contido num `parent` com diversos outros elementos semelhantes, evitando assim a declaração de diversas constantes para cada elemento.<br/>
No codigo acima,  ao clicarmos em qualquer lugar do `buttonContainer`, o console exibiria a mensagem "evento click", mas a condicional só sera satisfeita se o atributo `nodeName` do target for do tipo `BUTTON`.
