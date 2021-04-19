---
layout: post
title:  "React Hooks. #1 Hooks, State Hook e Effect Hook"
date:   2021-04-16 23:24:45 -0300
categories:  React Javascript
---

### O que são React Hooks?

Hooks são funções que nos permitem ligar os recursos do `state`e `lifecycle` a partir dos `components` <br/><br/>

### Por que usar React Hooks?

Hooks permitem que você use o `state` sem escrever uma classe. Você pode extrair lógica de um `state`de forma que possa ser testada independentemente e reutilizada. Os Hooks permitem que você reutilize lógica com o `state`sem mudar a hierarquia de `components`<br/><br/>

### Regras dos Hooks
<ul>
<li>Chamar Hooks nos mais altos níveis. Isto é, não chamar dentro de condicionais, loops, ou funções aninhadas. Caso possível, use condicionais ou loops dentro do Hook </li><br/>
<li>Chamar os Hooks apenas em <strong>components</strong></li>
</ul> <br/>

### State Hook
O `useState` é um hook chamado dentro de um `component` para adicionar `states` locais a ele. O React preserva o `state` entre os re-renders.
O `useState`retorna o par `valor`e `setValor`, ou seja, o valor atual e uma função que atualiza o valor atual. O argumento para o `useState` é o state inicial. Veja o exemplo abaixo:

{% highlight jsx %}
import React, { useState } from 'react';

function Example() {
  // Declara uma nova variável de state, que chamaremos de "count"  
const [count, setCount] = useState(0);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
{% endhighlight %}<br/>

No exemplo acima, definimos o valor atual `count`, e a função que atualiza count `setCount`.
O argumento inicial do `useState` é 0, isso significa que `count` iniciará tendo 0 como valor de referência. <br/>

Ao clicarmos no botão, utilizamos o método que atualiza count `setCount` para adicionarmos 1 ao valor atual. Poderiamos também redefinir o state como bem entendessemos e fosse necessário. Isso seria necessário quando precisassemos de algum `state` de referência em alguma operação de comparação.
<br/><br/>
### Hook de efeito

O `useEffect`adiciona a funcionalidade de executar `side effects` através de um `component`, segue a mesma finalidade do `componentDidMount`, `componentDidUpdate` e `componentWillUnmount` usadas em classes, mas unificado em uma mesma API.  O React ira se lembrar da função passada e chama-la depois de atualizar o DOM<br/>

Utilizando o exemplo acima 
{% highlight jsx %}
import React, { useState, useEffect } from 'react';

function Example() {
   
const [count, setCount] = useState(0);
useEffect(()=>{
    // Atualiza o título do documento
    document.title = `you clicked ${count} times`
})
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
{% endhighlight %}<br/>

Quando chamamos a `useEffect`, estamos dizendo ao React para executar a função após liberar as mudanças para o DOM. Por padrão, o React executa os efeitos após cada renderização<br/>
O `useEffect`executa depois de <strong>toda</strong> renderização, mas podemos mudar isso com a seguinte otimização:

{% highlight jsx %}
const [count, setCount] = useState(0);
useEffect(()=>{
    // Atualiza o título do documento
    document.title = `you clicked ${count} times`
},[count]) //apenas re-executa o efeito quando count mudar
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
{% endhighlight %}<br/>

O que significa isso? Significa que estamos dizendo ao React para pular o efeito caso `count`não se atualize. <br/><br/>
<strong> Observações sobre a otimização do `useEffect`: </strong> 
<ul>
    <li>
        É necessário que o array inclua qualquer valor do escopo acima, como props ou `state`, que mude com o tempo, caso contrário, o useEffect não irá funcionar corretamente.
    </li>
    <li>
        Caso queira executar o efeito e limpá-lo apenas na montagem e desmontágem, um array vazio [] pode ser usado como segundo argumento. Isto mostra ao react que seu efeito nao depende de valor props ou state.
    </li>
</ul>
