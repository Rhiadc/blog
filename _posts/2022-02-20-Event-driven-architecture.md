---
layout: post
title:  "Patterns arquiteturais: Event Driven Architecture"
date:   2022-02-21 13:24:45 -0300
categories: Go Arquitetura EDA
---

<img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRr92s2eJrif3ikuUTmxRY6YVRYnTXCedklyA&usqp=CAU"  height="250" style="display: block; margin: 0 auto;"> <br/>

Gosto de pensar em um evento como um registro de algo que aconteceu em algum momento no passado (1 hora, 10 horas, 1000 anos, ou até mesmo 0.01 segundo no passado). Eventos são imutáveis, e eles são naturalmente ordenados por ordem de execução no tempo. Nos ultimos tempos, a granularidade e quantidade de eventos associados a um fluxo em específico aumentou exponencialmente. É natural que, com o passar dos anos e o evoluir das tecnologias, passemos a nos preocupar e tratar tais eventos de maneira muito mais granularizada, e aproveitemos da capacidade de processamento das ferramentas modernas para construirmos nossas soluções. Assim podemos dizer que sistemas modernos precisam reconhecer, assimilar e tratar eventos de maneira eficiênte e eficaz.

Dois aspectos importantes em uma fluxo arquitetural, se tratando de eventos, podem ser:
- <b> Responsividade:</b> pode ser visto como tempo que um usuário ou sistema tem de esperar entre uma operação e outra.
- <b> Performance:</b> Tempo demandado por um sistema para processar uma operação.

Nesse cenário, um sistema pode ser responsivo, mas não performatico.

Mas afinal, o que é uma EDA? Bem, gosto de pensar uma arquitetura event driven como uma esquematização composta por componentes (pensados como microserviços, combinado com esse outro estilo arquitetural) desacoplados, onde existe produção e tratamento de mensagens entre esses componentes, sempre sendo realizada de maneira assíncrona. Mas pra falarmos de event driven, é legal falarmos antes de request driven.

#### Request driven
De maneira simplificada, requisições são aplicadas por interfaces de usuário suportados por uma API. Requisições essas que são encaminhadas para handlers internos, que por sua vez, fazem consultas e/ou atualizações em bases de dados, com intuito de ter uma devolução (ou não) para o usuário ou sistema utilizador. Arquiteturas request driven apresentam desafios complexos de serem resolvidos, como escalar mantendo eficácia.

#### Event driven 
Uma arquitetura event driven se refere a um sistema fracamente acoplado entre si, que obedece um padrão de comunicação de troca de mensagens entre produtores e consumidores. Uma arquitetura event driven oferece a possibilidade de um produtor enviar a mensagem a um tópico, consumidor, filas e etc, depende muito da ferramenta utilizada (Kafka, padrão pub/sub, SQS que trigga uma lambda num ambiente serverless e por ai vai). Event driven é naturalmente reativo, isso é:
- <b>Responsivo</b>: pois sua comunicação é efetuada de maneira assincrona.
- <b>Resiliente</b>: pois existe interdependencia e provavelmente replicação de dados.
- <b>Elasticidade</b>: pois mantem um sistema desacoplado facilmente extensível.

A implementação e planejamento de uma arquitetura event driven é fortemente suportada por aspectos aplicados ao DDD. Atravez do DDD, temos suporte a uma delimitação de contextos bem definidos por meio dos bounded contexts, aggregates e etc. Também é extremamente recomendado avaliar o cenário antes de se adotar o EDA, se existe alta complexidade e forte necessidade de dinamismo, tendo que se garantir responsividade e resiliência, EDA pode ser uma excelente solução. Caso contrário, possivelmente não valerá a pena o preço de implementação e manutenção.

### Afinidades com arquitetura de microserviços e seus diversos patterns.
Uma grande gama de patterns de microservices podem ser aplicados à arquiteturas orientadas a eventos. 

Resumidamente, podemos citar algumas:
- <b>Sagas</b>: Transações de longas durações que podem ser escritas como sequencia de transações independentes e intercambiávbeis, onde cada ação tem uma ação complementar. Um clássico problema um sistema de reserva de passagem que oferece a possibilidade de se reservar um hotel e um carro num pacote. Caso algum desses serviços falhar individualmente, como garantir que todo o nosso fluxo do sistema falhe como um todo? Esse pattern trata diretamente disso. Existe basicamente dois modos de coordenação em saga: Coreografado e Orquestrado. O coreografado é basicamente onde temos um serviço local publicando uma mensagem para outro serviço, que por sua vez processa essa mensagem e publica uma resposta. Só ai o primeiro serviço verifica essa resposta e processa a transação como um todo. O segundo modelo é o Orquestrado, que basicamente vai oferecer um orquestrador que dirá aos participantes qual transação executar.
- <b> Event Sourcing</b>: Conta basicamente a "história" de uma entidade. O event sourcing persiste o estado de uma entidade de negócio, como por exemplo, um pedido, usuário ou transação em uma sequência cronológica de eventos de transformação de estado. Quando o estado de uma entidade muda, um novo evento é adicionado à lista de eventos. Assim, podemos ter um "snapshot" de uma entidade de negócio.
- <b> Outbox Pattern </b>: Pattern que visa garantia de entrega, nesse caso, utilizado em troca de mensagens entre sistemas ou parte dele, geralmente quando não podemos ter atomicidade na operação.  Ao invés de tentarmos uma ação direta não atômica (publicar uma mensagem), devemos focar em realizar operações atômicas que posteriormente nos permitam realizar uma ação específica desejada. Nesse cenário, podemos pensar em diversas tecnicas específicas para a realização dessa tarefa, como um polling numa tabela de referência, utilização de ferramenta de replicação de dados como Debezium e etc.
- <b> Domain events </b>: Organização de lógica de serviços na utilização de aggregates de DDD. A ideia aqui é gerar eventos na criação ou atualização de entidades de negócio. Ao realizar uma operação em uma entidade, geramos um evento e passamos ele pra frente, para que os outros serviços saibam que nosso domínio gerou um evento.

Enfim, podemos falar de diversos outros modelos de patterns, a ideia aqui era mostrar o quão poderosa pode ser essa arquitetura, e se bem estudada e implementada, pode resolver uma série de problemas de negócio. A EDA oferece pontos extremamente positivos, mas também pode cobra uma complexidade extremamente alta e custosa. Nesse cenário, como em qualquer outra tecnologia (funcionando como ferramenta), deve ser analisado a situação atual de negócio e se é viavel para o andamento do projeto.



#### fontes
https://developer.ibm.com/articles/advantages-of-an-event-driven-architecture/
https://microservices.io/patterns/data/domain-event.html
https://microservices.io/patterns/data/transactional-outbox.html
https://medium.com/@victorhsr/microservices-outbox-pattern-a4344d8ed0b
https://microservices.io/patterns/refactoring/anti-corruption-layer.html
https://developer.ibm.com/articles/eda-and-microservices-architecture-best-practices/