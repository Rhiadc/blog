---
layout: post
title:  "Eficiência usando funções de programação funcional como reduce e map "
date:   2021-04-01 23:24:45 -0300
categories: Javascript 
---

Embora a `map` e a `reduce` possam ser utilizadas com o mesmo proposito em algumas situações, é importante verificarmos em qual situação cada função se mostra mais eficiênte

Enquanto o `map` invoca uma função callback passada por argumento para cada elemento do Array e devolve um novo Array como resultado, o `reduce` devolve um valor decorrente do array, posterior à aplicação da função callback.<br /><br />

### Usando o map() 
Vejamos um exemplo, neste caso, queremos obter uma string de elementos HTML para inserirmos no nosso DOM. Seja o array:
{% highlight javascript %}
const alunos = 
[
    {
        id:1,
        nome: "Marisa"
    },
    {
        id:2,
        nome: "Sara"
    },
    {
        id:3,
        nome: "Felipe"
    }
]
{% endhighlight %}
<br />
Para gerarmos uma string com todos os nomes dentro de um elemento `<li>` podemos fazer o seguinte, utilizando a função  `map()`

{% highlight javascript %}
let geraListaAlunos = alunos.map(aluno=>{
    return `<li>${aluno.nome}</li>` 
})
console.log(geraListaAlunos)
{% endhighlight %}
<br />
O resultado do `console.log` acima é o seguinte resultado: 
![consolelog](https://images2.imgbox.com/2c/0a/mFpewmqS_o.png)<br />
Ou seja, um array de strings. Para gerarmos uma string única, devemos aplicar um `.join('')`no `geraListaAlunos`

{% highlight javascript %}
let geraListaAlunos = alunos.map(aluno=>{
    return `<li>${aluno.nome}</li>` 
})
geraListaAlunos = geraListaAlunos.join('')
console.log(geraListaAlunos)
{% endhighlight %}
<br />
O resultado, depois de usarmos o `.join('')`:
![consolelog](https://images2.imgbox.com/e7/bc/AFFEqkqI_o.png)<br /><br /><br />

### Usando o reduce() 
Usando o mesmo array utilizado acima, vejamos o mesmo procedimento agora usando o `reduce()` , com o parâmetro `acumulador`, setado para `''`
{% highlight javascript %}
let geraListaAlunos = alunos.reduce((acumulador,item)=>{
    acumulador += `<li>${item.nome}</li>`
    return acumulador
},'')
console.log(geraListaAlunos)
{% endhighlight %}
<br />
O resultado:<br />
![consolelog](https://images2.imgbox.com/e7/bc/AFFEqkqI_o.png)<br />
Ou seja, chegamos ao mesmo resultado quando usamos o `map()`, porém de maneira mais eficiênte e enxuta


