---
layout: post
title:  "Injeção de dependência em Go"
date:   2021-12-07 13:24:45 -0300
categories: Go Golang
---


Trabalhar com Go, inevitavelmente acaba nos retornando às ideias de arquitetura limpa pregadas pelo Uncle Bob. Conceito esse que, de maneira geral, consiste em separar seu software em camadas, criando um sistema intrinsecamente testável e que, caso alguma camada desse sistema se torne obsoleta, como por exemplo, uma database, ela seja facilmente substituida por outra, dando o mínimo de trabalho possível. <br/>

Um dos principios mencionados pelo Uncle Bob em seu livro, é o <strong>Princípio da dependência</strong>. Esse principipio diz que, algo declarado no circulo exterior, não deve ser mencionado no código por um círculo interior. </br>

Por exemplo, a camada de domínio não pode depender da camada de infra, mas a camada de infra pode depender na camada de domínio (por que faz o caminho inverso) ![dominio](https://khalilstemmler.com/img/wiki/dependency-rule/the-dependency-rule.svg)
