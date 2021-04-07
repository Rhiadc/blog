---
layout: post
title:  "Usando o localStorage para armazenamento de dados não sensíveis no Javascript"
date:   2021-04-02 23:24:45 -0300
categories: Javascript
---

A API Web Storage é muito interessante no armazenamento de dados não sensíveis. Apresenta 2 propriedades: o `sessionStorage`, que armazena dados gravados até o termino da sessão do usuário, e o `localStorage`
que armazena os dados "permanentemente" no navegador, sendo possível limpa-lo apenas através do javascript, limpando o cache do navegador ou manualmente na aba armazenamento do painel desenvolvedor do navegador.

De maneira simples, a estrutura de dados é a seguinte: <br />
{% highlight javascript %}
{
    key: 'value'
}
{% endhighlight %}<br />

E apresenta 4 funções:  <br />
#### localStorage.setItem()
{% highlight javascript %}
{
    const updateLocalStorage = () =>{
        localStorage.setItem("Maçãs": 3 )
    }
}
{% endhighlight %}
Aqui, simplesmente declaramos uma key e associamos a um valor<br /><br />

#### localStorage.getItem()
{% highlight javascript %}
{
    const getItemLocalStorage = () =>{
        localStorage.getItem("Maçãs")
    }
}
{% endhighlight %}
recuperamos o valor do storage a partir da key<br /><br />
#### localStorage.removeItem()
{% highlight javascript %}
{
    const removeItemLocalStorage = () =>{
        localStorage.removeItem("Maçãs")
    }
}
{% endhighlight %}
Removemos o item a partir de sua key do storage<br /><br />

#### localStorage.clear()
{% highlight javascript %}
{
    const removeLocalStorage = () =>{
        localStorage.clear()
    }
}
{% endhighlight %}
Onde todas as chaves de armazenamento serão esvaziadas<br /><br /><br />

Para aplicações simples onde o contato com uma db não é muito apropriado (o localStorage guarda até 5MB), a API pode quebrar um galho.



