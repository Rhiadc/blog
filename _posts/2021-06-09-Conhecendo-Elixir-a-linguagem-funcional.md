---
layout: post
title:  "Elixir: o que é e motivos para aprender"
date:   2021-06-09 01:24:45 -0300
categories:  Elixir
---
<img src='http://www.each.usp.br/petsi/jornal/wp-content/uploads/2018/09/elixir.png'>
>Elixir é uma linguagem dinâmica e funcional, projetada para construir aplicações escaláveis e de fácil manutenção.

<br/>
- <strong>Funcional</strong>: paradígma de programação que utiliza o conceito de funções puras/retornos de dados que dependem apenas de seus parâmetros (stateless, imutável, composição de funções e high-order functions).

- <strong>Distribuido</strong>: Divide-se uma aplicação em dezenas, centenas e até milhares de diferentes aplicações que se comunicam entre si e compartilham recursos (responsividade, eficácia, rapidez).

- <strong>Orientado a processos</strong>: delimita a separação de dados e dos métodos que agem sobre eles. (paralelização mais eficiênte e organizada).

- <strong>Concorrente</strong>: Diversas tarefas executadas num mesmo período de tempo (ex: 1 core, 2 processos).



- O <strong>Elixir</strong> usa a máquina virtual do Erlang e a sua capacidade de criar aplicações distribuidas de baixa latência. (inclusive podemos usar funções do Erlang)

- O elixir é <strong>escalável</strong>: o Elixir acompanha o crescimento da sua aplicação.

- É <strong>tolerante a falhas</strong>: Se um nó morre, a maquina virtual do Erlang consegue cuidar disso para que não haja falhas na aplicação toda, o nó é restartado depois.

- É uma linguagem <strong>funcional</strong>: isso garante que a concorrência seja um dos focos da linguagem. 

- É <strong>extensível</strong>: isso significa que conseguimos criar módulos novos de acordo com nossas necessidades.

<br/><br/>

### Conhecendo o Mix 

O mix é o gerenciados de projetos, tasks e dependências oficial da linguagem. Apesar de ser nova, a linguagem conta com várias bibliotecas open-source, e aumenta a cada dia. Praticamente tudo o que precisamos, podemos achar no Package Manager oficial.<br/><br/>

Conhecendo alguns comandos do Mix: <br/><br/>
Busca as dependências definidas no projeto.
{% highlight shell %}
mix deps.get
{% endhighlight %} <br/> 
Cria um novo projeto.
{% highlight shell %}
mix new example
{% endhighlight %} <br/> 
Roda os testes da nossa aplicação (a comunidade Elixir também se preocupa com testes).
{% highlight shell %}
mix  text
{% endhighlight %} <br/> 
Novo projeto como framework Phoenix.
{% highlight shell %}
mix phoenix.new
{% endhighlight %} <br/> 


