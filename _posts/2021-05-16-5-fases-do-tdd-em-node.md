---
layout: post
title:  "5 passos de TDD em aplicações NodeJs"
date:   2021-05-16 05:24:45 -0300
categories:  Nodejs TDD
---

Existem 5 passos num fluxo TDD: <br/>
 - Ler, entender e processar a feature ou bug.
 - Traduzir um requerimento escrevendo um unit test, se estiver usando um setup hot reloading, os testes vão falhar, visto que o código prioritário ainda não está implementado.
 - Escrever e implementar o código que satisfaz os requerimentos. Rodar os testes, eles devem passar. Se falharem, repetir o passo.
 - Limpar o código fazendo um refactor.
 - Aperfeiçoar.

 <img src='https://developer.ibm.com/developer/default/articles/5-steps-of-test-driven-development/images/tdd-red-green-refactoring-v3.png' >
<br/>

O workflow acima é o conhecido RGR, ou Red-Green-Refactor, e suas fases significam:
 - <strong>Vermelho</strong>: Código não funciona.
 - <strong>Verde</strong>: Tudo funcionando, mas necessário otimizar.
 - <strong>Azul</strong>: Tudo ok, podemos aplicar refatoramento e melhorar nosso código.
<br/>

Unit tests também são parte integral do CI/CD e fazem parte do deployment pipeline, se os testes passarem, a integração e deploy acontecerá. <br/><br/>

### Ferramentas e libs
Como estamos usando Node, as libs:
 - <strong>Jest</strong> - para testes de unidade.
 - <strong>ESLint</strong> - para análise de código.
 - <strong>Standard</strong> - para formatação de código.
 - <strong>Husky</strong> - para hooks precommit de Git.


### Decidindo quando escrever testes de unidade
Geralmente, existem dois casos onde queremos escrever os testes de unidade: <br/>
<strong>Caso A: </strong>escrevemos um teste de unidade para representar uma funcionalidade concisa na nossa aplicação. Exemplo: uma funcionalide que retorna o numero de criptomoedas listados por determinada exchange. A primeira coisa a se fazer é escrever um unit test que falha, e depois escrever/mudar o nosso código até que ele passe nos testes. <br/>
<strong>Caso B: </strong> um pedaço de código em prod quebra, o bug apresentado nesse código altera alguma funcionalidade da aplicação. Nesse caso, implementamos um teste de unidade e reescrevemos nossa funcionalidade até ela funcionar corretamente. Nesse caso teremos dois benefícios de implementar o unit test, a reusabilidade do teste e a correção do bug.

### Conclusão
É essencial usarmos TDD nas nossas aplicações. TDD, embora aparentemente mais custoso de implementar no começo (aparentemente, pois evitar retestes ao implementar novas funcionalidades evita também muito tempo perdido) nos garante um código melhor, mais reutilizável e de mais fácil manutenção.

