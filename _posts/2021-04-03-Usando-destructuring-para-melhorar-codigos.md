---
layout: post
title:  "Usando destructuring para melhorar codigos"
date:   2021-04-03 23:24:45 -0300
categories: Javascript Refactoring
---

Tome o seguinte pedaço de código: <br />
{% highlight javascript %}
 data.map(song =>
	const artistName = song.artist.name
	const songTitle = song.title
                ...
{% endhighlight %}<br />	

Podemos fazer um destructuring na referência `song`... `artist` da primeira const, `title` na segunda, veja:
{% highlight javascript %}
data.map(({artist, title})	 =>
	const artistName = artist.name
	const songTitle = title
                ...
{% endhighlight %}<br />

podemos ainda ir além e declarar outro destructuring derivado da `artist`, veja só: 
{% highlight javascript %}
data.map(({artist:{name}, title}) =>
	const artistName = name
	const songTitle = title
                ...
{% endhighlight %}<br /><br /><br />

Um segundo exemplo do poder do destructuring: 
{% highlight javascript %}
const doSomething  = pessoa => {
  	const name = pessoa.name  
	const age = pessoa.age
	            ...

{% endhighlight %}
no exemplo acima, a função recebe um objeto `pessoa` como argumento, e atribui à constante `name` e `age` os atributos `name` e `age` do objeto `pessoa`... Essa função é similar a seguinte:
{% highlight javascript %}
const doSomething  = ({name, age}) => {
  	            ...

{% endhighlight %}<br/>
desse modo, nem precisamos declarar as variáveis `name` e `age`, visto que elas ja vieram como parâmetros no destructuring do objeto.
Podemos observar que poupamos muitas linhas de código, deixando-o mais enxuto e legível
