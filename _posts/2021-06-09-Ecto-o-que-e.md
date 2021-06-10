---
layout: post
title:  "Ecto: interagindo com dbs em Elixir #1"
date:   2021-06-09 01:24:45 -0300
categories:  Ecto Elixir
---

> Ecto é uma linguagem específica de domínio (DSL) para escrever queries e interagir com databases em Elixir.

<br/>
Ecto é uma linguagem específica de domínio para escrever queries e interagir com databases em Elixir. Ou seja, o ecto fica em uma camada entre o banco de dados e a sua aplicação. <br/>

O Ecto não é um <strong>ORM</strong>, ORM trata de objetos, e por Elixir ser uma linguagem funcional, a ideia é mapearmos a fonte de dados conforme a nossa necessidade.<br/>

O <strong>Ecto</strong> é baseado no <strong>LINQ</strong> (Language Integrated Query, parte do .NET). Exemplo de query no Ecto

{% highlight elixir %}
(from c in Course,
    join: class in assoc(c, :classes),
    join: enrollment in assoc(class, :enrollments),
    where: enrollment.status == "completed" or enrollment.status == "dismissed",
    select: enrollment)
{% endhighlight %} <br/> 

### Composição
{% highlight elixir %}

#composição de duas regras usando a mesma query
query = from u in "users",
        where: u.age > 18,
        select: u.name

query = from u in query
        order_by: u.name
{% endhighlight %} <br/> 

Isso é possível por causa dos `protocols` de Elixir. Mas o que seria `protocol`?  O <strong>Elixir</strong> provêm protocolos como um mecanismo para polimorfismo. São parecidos com interfaces. Basicamente, dizemos que queremos trabalhar com determinados tipos de dados, desde que eles implementem protocolos determinados.

### Sobre os dados

- evita SQL injections (por ser uma DSL e apresentar queries sanitizadas).

- Interpolação e Casting de dados.

- Valores externos e expressões em Elixir podem ser adicionadas a uma query com `^`

{% highlight elixir %}
#com schema definido
def with_minumun(age) do
    query = from u in "users",
            where: u.age > ^age,
            select: u.name
end           

#sem schema definido
def with_minimun(age) do
    query = from u in "users",
    where: u.age > type(^age, :integer),
    select: u.name
end
{% endhighlight %} <br/> 

- Type safety: 
{% highlight elixir %}
name == 1

#retorna erro (Ecto.Query.TypeCheckError) the 
#following expression does not type check
{% endhighlight %} <br/> 

- Componentes
    - Repo
    - Schema
    - Changeset
    - Query

