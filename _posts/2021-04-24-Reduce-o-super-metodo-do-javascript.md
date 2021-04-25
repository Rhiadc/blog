---
layout: post
title:  "Reduce - O super método do Javascript (e como usá-lo ao seu favor)"
date:   2021-04-24 23:24:45 -0300
categories: Javascript 
---

De modo similar aos métodos `map` e `filter`, o `reduce` também é uma high order function que recebe uma função por argumento e executa essa função para cada item do array. Usamos o `reduce` quando precisamo reduzir o array a um tipo de dado, como um objeto literal, uma string, um número ou até mesmo um outro array. Ao contrário do `map` e `filter`, o `reduce` tem poder de gerar um output diferente de um array<br/>

{% highlight javascript %}
Exemplo simples de funcionamento do reduce:

const number = [1,2,3]
const sum = numbers.reduce((accumulator, item)=> accumulator + item , 0)
{% endhighlight %}

Na função acima, `accumulator` parte de 0 e tem a ele somado o primeiro item do array (1), na proxima execução da função, `accumulator` (que agora é 1) é somado com 2, e na proxima execução, somado com 3... Ou seja, o parâmetro retornado pela função é armazenado em 
`accumulator` a cada iteração.<br/><br/>

Outro exemplo de caso em que podemos usar o reduce:
{% highlight javascript %}
const cart = {
	{name: 'Dark Souls III', price: 60.00},
	{name: 'Sekiro: Shadows Die Twice', price: 80.00},
	{name: 'The Witcher 3', price: 40.00}
}

const productList = card.reduce((accumulator, { name })=>
 `${accumulator} ${name} `,'')
{% endhighlight %}<br/>

na primeira execução, `accumulator` recebe a string vazia '', e name recebe "Dark Souls 3". 
Então a função retorna, na primeira execução -> 'Dark Souls 3'<br/>
Na segunda execução `accumulator` armazena -> ' Dark souls 3', name armazena -> 'Sekiro: Shadows Die Twice'.
Então a função retorna, na segunda execução -> 'Dark Souls 3 Sekiro: Shadows Die Twice'. 
...e assim por diante
A partir dai, podemos manipular os dados da maneira que precisarmos <br/>


Um outro exemplo mais interessante:

{% highlight javascript %}
const people = [
	{id:1, name:'Mauricio', age:18},
	{id:2, name:'Roger', age:24},
	{id:3, name:'Felipe', age:18},
	{id:4, name:'Juliana', age:40}
]

/* Caso queiramos retornar um objeto com a frequencia das idades
do tipo {18:2, 24:1 40:1}
podemos utilizar mais uma vez o reduce*/

const agesFreq = people.reduce((accumulator, { age }) =>{
	accumulator[age] = accumulator[age] + 1 || 1
	return accumulator
}, {})

{% endhighlight %}<br/>
No `reduce` acima, o `accumulator` assume papel de objeto, assim, criamos a key `age` no `reducer`, e a cada iteração somamos 1 ao valor referente a key de cada objeto recorrente. Caso o objeto ainda nao tenha sido referenciado, usamos o operador logico `||` para atribuirmos o valor 1 à key */