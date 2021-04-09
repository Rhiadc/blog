---
layout: post
title:  "React router e uso do Switch "
date:   2021-04-08 23:24:45 -0300
categories: Javascript React
---


Em App.js, dentro do return da Classe App

{% highlight jsx%}
<Router>
	<Navbar title ={this.state.title} icon ={this.state.icon}/>
    <Route exact path='/User' component={User}/>
	<Switch>
		<Route exact path='/about' component = {About}>
	</Switch>
</Router>
{% endhighlight %} <br />
Como o `Switch` pode ser diferente de usar vários `Routes`?
o `Switch` renderiza uma rota por vez, de maneira exclusiva, assim como o nome sugere. Em comparaçao com o `Routes` que as renderiza as rotas ao mesmo tempo, de maneira inclusiva;<br />

No exemplo do código acima, as rotas `<Navbar />` e `<User />` serão renderizadas ao mesmo tempo, enquanto a rota `<About />` sera renderizada separadamente. Neste caso, usando uma `Route`fora do `Switch` nos permite criar elementos persistentes na tela.
