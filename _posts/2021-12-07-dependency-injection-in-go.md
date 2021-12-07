---
layout: post
title:  "Injeção de dependência em Go"
date:   2021-12-07 13:24:45 -0300
categories: Go Golang
---


Trabalhar com Go, inevitavelmente acaba nos retornando às ideias de arquitetura limpa pregadas pelo Uncle Bob. Conceito esse que, de maneira geral, consiste em separar seu software em camadas, criando um sistema intrinsecamente testável e que, caso alguma camada desse sistema se torne obsoleta, como por exemplo, uma database, ela seja facilmente substituida por outra, dando o mínimo de trabalho possível. 

Um dos principios mencionados pelo Uncle Bob em seu livro, é o <strong>Princípio da dependência</strong>. Esse principipio diz que, algo declarado no circulo exterior não deve ser mencionado no código por um círculo interior. 

Por exemplo, a camada de domínio não pode depender da camada de infra, mas a camada de infra pode depender na camada de domínio (por que faz o caminho inverso, daí usamos o DIP) <p align="center">
![dominio](https://khalilstemmler.com/img/wiki/dependency-rule/the-dependency-rule.svg)
</p>

Mas, qual a necessidade? Sem entrar muito detalhes: Para evitarmos dependências circulares, mantermos o código testável, limpo e sustentável.

Mas agora indo pra parte do código, como o Go facilita isso pra gente?

A ideia de injecão de dependência (DI) vem do fato que nossos componentes (nossas structs em Go) devem receber suas dependências na hora de sua criação

Suponhamos que temos uma struct 
`Server`, e essa struct requeira uma `Config` para poder ser implementada. Uma maneira a se fazer isso é do seguinte modo:

```go
type Server struct {
    config *Config
}

func NewServer() *Server {
    return &Server{
        config: buildMyConfig(),
    }
}

```

Deste modo, o constructor de `Server` inicia sua propria config diretamente. E está tudo certo. Nosso caller não precisa saber que o `Server`tem ligação com a `Config`. Tudo escondido do usuário, né?

Bom, não é bem assim. Imagine em um cenário hipotético (muito provável, por sinal), que nos quisessemos trocar o comportamento de nossa `Config`, nos deveriamos fazer a manutenção em todos os cantos que ela está definida. Podemos também querer incrementar nossa função `buildMyConfig`, o que levaria-nos a um efeito dominó de mudanças sem fim, em um cenário mais complexo de uma aplicação real.

Agora, podemos solucionar este problema do seguinte modo:

```go
type Server struct {
    config *Config
}

func NewServer(config *Config) *Server {
    return &Server{
        config: config,
    }
}
```

E no nosso caller, teriamos algo parecido com isso:

```go
    var (
        httpConfig network.Config = network.NewConfig()
        httpServer http.Server = http.NewServer(httpConfig)
    )
```

A diferença é perceptível, não? Nosso `Server`agora está desacoplado da nossa `Config`, e não só isso, caso `Config` fosse uma interface, bastariamos passarmos qualquer tipo que implementa essa interface como parâmetro da função `NewServer` que tudo funcionaria normalmente.

Mas, e quais pontos negativos essa abordagem tem? Bom, pe muito trabalhoso criar manualmente a `Config` antes de criarmos o `Server` (pois nesse caso, o `Server` precisa da `Config` para ser iniciado). Numa aplicação real, essas dependências podem cresçer de maneira abrupta e se tornar algo complexo de manter. 

#### Conclusão
A injeção de dependência ajuda a construirmos aplicações mais robustas e confiáveis. A natureza de construção desacoplada que a Golang nos proporciona facilita tomarmos decisões arquiteturais baseadas em negocio, em uma aplicação complexa. Existem alguns outros conceitos como `DI frameworks` (que facilitam a vida na construção de injeções de dependência), `containers` de DI, e até mesmo o container de DI oferecido pelo Google, o [Wire](https://github.com/google/wire). São ideias que facilitam a implementação de DI e tornam ainda mais lucrativo a utilização de tais ideias.

#### Referências
- https://khalilstemmler.com/wiki/dependency-rule/
- https://campuscode.com.br/conteudos/s-o-l-i-d-principio-de-inversao-de-dependencia
- https://blog.drewolson.org/dependency-injection-in-go
- https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html