---
layout: post
title:  "Aprofundando em aspectos de clean architecture e domain driven architectures"
date:   2022-02-16 13:24:45 -0300
categories: Go Arquitetura
---
 <img src="https://res.cloudinary.com/practicaldev/image/fetch/s--Mm0JJoP9--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/uploads/articles/00zocjrfniog06hsbwnr.jpeg"  height="300" style="display: block; margin: 0 auto;">
<br/>
### Motivação



É comum a propagação de ideias e conceitos relacionados a planejamento e arquitetura de software. O fluxo de informações hoje trouxe a tona diversos conceitos relacionados a engenharia de software, tais quais conceitos fortes de arquitetura como Onion, Hexagonal, Clean, e até mesmo menções ao DDD em uma arquitetura domain driven. As vezes é até meio obvio que devemos usar e estudar modelos de arquiteturas, mas nem sempre fica claro o porquê de escolhermos usar determinado modelo, e quais benefícios isso trará

<p style = "text-align: center;"> Produtividade x Período de tempo</p>
<img src="https://imgur.com/APGz5SM.png"  height="250" style="display: block; margin: 0 auto;"> <br/><br/>

<p style = "text-align: center;"> Custo x Entrega</p>
<img src="https://imgur.com/yjUf6le.png" height="250" style="display: block; margin: 0 auto;"> <br/>

Ao analisarmos alguns gráficos, montados a partir de informações reais obtidas de uma empresa anônima (retiradas do livro arquitetura limpa), podemos observar que existe uma grande defasagem. O desempenho do time começa alto, mas com o passar do projeto, esse desempenho vai caindo quase que de maneira assintotica. Já em relação ao segundo gráfico, vemos o custo do projeto aumentar, enquanto que o desempenho do time acaba por se manter estagnado. Podemos pensar que esse problema poderia ser revolvido com a adição de mais desenvolvedores (o que por si só, já geraria um outro problema no mercado atual), mas nem com a adição de novos desenvolvedores resolveriamos tais problemas. Isso se dá pelo fato que, contratar novos desenvolvedores não só custa altos valores monetários, mas requer desvio de atenção do time e também uma integração necessaria (imersão, onboarding) que o desenvolvedor precisará ter acerca do projeto o qual trabalhará. As vezes o projeto já é tão grande, complexo e desorganizado que tal integração demoraria tempos inimaginaveis.
Tendo em vista esse ponto, nos resta levantar tal questão: "O que causa todos esses problemas observados no gráfico?". Será que os desenvolvedores param de trabalhar? Se acomodam? Bem, as informações dizem o contrário. É nesse período de tempo que os desenvolvedores mais trabalham e mais se sentem cansados. É a falta de planejamento e estruturação das ideias aplicadas ao modelo a ser desenvolvido que cobra esse preço. A forte estruturação desde o começo evita que o projeto vire uma bola de néve com o tempo. Não se evita gasto de tempo, apenas se adia, contribuindo ainda mais para um gasto ainda maior num futuro, tanto para o time, quanto para o proprio desenvolvedor. São esses gastos que fazem parte de um gargalo mortal no desenvolvimento do projeto. É por isso que é tão importante estudarmos a arquitetura de software e designs de projeto. <br/><br/>

#### Conceitos importantes de SOLID

<img src="https://nahidsaikat.com/media/pixabay/solid-principle.jpg" height="350" style="display: block; margin: 0 auto;">
<br/>

Antes de prosseguir, é interessante termos em mente, de maneira simplificada, como alguns conceitos do SOLID se interligam com o desenvolver da arquitetura.

<b> SINGLE RESPONSABILITY PRINCIPLE. </b>
De maneira geral, a estrutura do sistema deve ser influenciada pela estrutura social da organização que o utiliza. Isso é, cada modulo de software deve ter uma e apenas uma razão para mudar.
De acordo com o Uncle Bob, esse é um dos principios menos compreendidos, isso se dá por causa do nome. Esse princípio não significa fazer com que nossos módulos façam apenas uma coisa, mas sim que sejam responsáveis apenas por um ator. Em outras palavras, uma e apenas uma razão para mudar.

Um clássico exemplo:
A equipe de RH e de Financeiro utiliza um algoritmo em comum, pertencente à função calcualteHours, para realizar coisas distintas. A equipe de RH usa o CalculateHours pra fazer os calculos de pagamento do time, enquanto o time de Financeiro utiliza essa função para realizar calculos de gastos e possíveis otimização. Imagine agora que essa função já não seja mais satisfatória pro time de financeiro, e que ele precise alterar o algoritmo da calculateHours. Agora, o time do RH não consegue mais calcular as horas de maneira adequada a sua responsabilidade. o SRP diz para separarmos esses codigos, isolando-os assim da responsabilidade um do outro.

<b> OPEN CLOSED PRINCIPLE </b>
Para que os sistemas de software sejam fáceis de mudas, eles devem ser projetados de modo a permitir que o comportamento desse sistema mude pela adição de novo código, e não pela alteração de um já existente


<b> DEPENDENCY INVERSION PRINCIPLE </b>
O código que implementa uma política de alto nível não deve depender do código que implementa detalhes de nível mais baixo. Em outras palavras, detalhes devem depender de políticas.
Arquitetura de software estáveis são aquelas que evitam depender de implementação concreta, e que favorecem o uso de interfaces abstratas estáveis.

Um exemplo clássico é: Uma entidade de domínio não deve conhecer ou se relacionar (diretamente) com o banco de dados. Porém, a implementação de um banco pode conhecer e se relacionar com o domínio. Seguindo esse principio, criamos modelos agnosticos de desacoplados, estruturalmente falando. Não queremos que componentes que mudem frequentemente e por motivos futeis afetem componentes que, em regra, deveriam ser estáveis.

OBS: nem todo componente deve ser estável.

nesse caso: 
- <b>Estável</b>: responsabilidade e independencia (Entidade)
- <b>Instável</b>: irresponsabilidade e dependencia (Banco)

<br/>

### Arquitetura

A arquitetura de um sistema pouco tem a ver com a relevância para o seu funcionamento. Existem muito sistemas por ai que funcionam bem, embora tenham uma terrível arquitetura. Os problemas não estão exatamente na operação, eles ocorrem sempre na implementação, manutenção e desenvolvimento continuado.

O propósito principal da arquitetura é suportar ciclo de vida do sistema, Uma boa arquitetura torna o sistema fácil de entender, fácil de desenvolver, fácil de manter e fácil de implementar. Uma sistema de software dificil de desenvolver provavelmente não terá uma vida útil longa e sustentável. Nesse caso, quanto maior o custo de implementação, menos útil o sistema. Isso resulta em sistemas fáceis de desenvolver, mas difíceis de se implementar.

A arquitetura também deve ditar as necessidades operacionais do sistema.
<br/>
<img src="https://archtrends.com/blog/wp-content/uploads/2012/06/tito-ficarelli-casa-10-604x590.jpg" height="350" style="display: block; margin: 0 auto;">

Ao olharmos à planta de uma casa, é fácil enxergarmos uma janela, uma porta, determinado cômodo. Ou até mesmoque tipo de planta é aquela. Uma arquitetura de software deve seguir os mesmos padrões. Devemos olhar para a aplicação e enxergarmos do que ela se trata, o que ela faz, e quais são suas responsabilidades. Para isso, devemos focar o software em dois elementos centrais.

<br/>

### Políticas e detalhes.

As políticas são as regras de negocio. É a partir das políticas que conseguimos identificar como nosso sofware vai se comportar, e o porquê. É na política que reside toda a alma do sistema.

Os detalhes são os itens que fornecem o meio de comunicação entre outros sistemas, usuários e outros programadores com a política.

É importante salientar também que, geralmente priorizamos as políticas no começo do planejamento arquitetural. Nos focamos nas resoluções de negocio. Não é necessário escolher um sistema de banco de dados no início do planejamento, nem o servidor web, adotar REST, microsserviços, SOA e etc. Essas são decisões que virão com facilidade quando a ideia da política estiver bem definida. A ideia aqui é adiar decisões sobre detalhes a fim de ter um leque de informações maior, no propósito de facilitar o escolhimento desses detalhes.

Uncle Bob diz que uma boa arquitetura deve possibilitar que um sistema nasça como monolito, seja implementado em um único arquivo e então cresça como um conjundo de unidades independentes implementáveis, incluindo serviços independentes e microsserviços.

<br/>

### Arquiteturas domain Driven - Clean


Nas ultimas décadas, pudemos observar o estudo acerca de algumas arquiteturas nascendo. Sendo elas Hexagonal, Onion, Clean e etc. Embora todas elas variem de alguma forma em seus detalhes, todas elas são muito similares. Todas tem o mesmo objetivo: a separação da preocupação. Todas realizam essa separação ao dividirem o software em camadas. Cada uma tem, pelo menos, uma camada para negocio, para interface de usuário e sistema.

É padrão observarmos nessas arquiteturas:
- <b> Independência de framework</b>: não adaptamos nosso sistema às restrições do framework, o framework é uma ferramenta.
- <b>Testabilidade</b>: regras de negocio não necessitam de UI, banco, servidor ou qualquer outro elemento pra ser testado.
- <b>Independencia da UI</b>: A ui não deve importar na hora de desenvolvermos nossos sistema, podemos ter uma aplicação console ou web sem alterar nossas regras.
- <b>Independencia de banco</b>: Devemos poder trocar Posgres por Mongo ou qualquer outra coisa, desde que satisfaça nossa especificação.
- <b>Independencia de agente externo</b>: Nossas regras de negocio não devem saber nada sobre interfaces do mundo externo.

<img src="https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg" height="350" style="display: block; margin: 0 auto;">

Os circulos mais externos são mecanismos, detalhes. Os mais internos são s políticas. As dependências do codigo fonte dedvem apontar apenas para dentro na diração das políticas de nível mais alto. Um exelemto de um circulo interno não podem saber nada sobre os elementos de um circulo externo. Em outras palavras, o nome de um elemento declarado em um circulo externo não deve ser mencionado por um circulo interno (ex: não podemos chamar um banco ou usecase na nossa entidade)

<br/>

### Entidades
As entidades são a essência da nossa aplicação. São os componentes cruciais relacionados às regras de negocio da empresa. Uma entidade pode ser um objeto, pode conter conjunto de métodos, especificações. A entidade contem o menor nível de fragmentação, e também possui forte nível de estabilidade. Nenhuma mudança operacional na aplicação deve influenciar na entidade. Fazendo um paralelo com o DDD:

<img src="https://miro.medium.com/max/732/1*P4zm6LF9l0nRmyN2iqjgHQ.png" height="350" style="display: block; margin: 0 auto;">

<br/>

Podemos relacionar a entidade como menor componente do Domain. É dentro do Domain que podemos ter a separação de componentes referentes às entidades. Também podemos comparar o Domain como o núcleo de um átomo. Isso porquê é estável, e muito menos propenso à modificações.



Podemos ter outros elementos no nosso domínio, como value objects, (email, point), enums (tipoPagto). Assim, podemos dizer que temos todos os conceitos do negócio (política) mapeadas no nosso domínio
**obs: entidades de domínio não tem relacionamento com entidades de banco.

<br/>

### Usecases

As políticas estão intimamente ligadas aos nossos usecases, é por meio dos usecases que fazemos publico a intenção do nosso sistema. A raiz do nosso projeto deve "gritar" a intenção do mesmo. Os casos de uso são responsáveis por esse papel. Isso é, um sistema de assistência médica deve gritar o que é. Quando novos desenvolvedores trabalharem o repo, devem ter a impressão que estão trabalhando em um sistema de assistência médica. Conseguimos isso por meio de usecases bem estruturados e que refletem a intenção do sistema e de sua política.
Os casos de uso orquestram o fluxo de dados para a partir das entidades e orientam essas entidades na aplicação nas regras de negocio. Os usecases devem fazer a ponte entre entidades e agentes externos, como os gateways (como veremos mais adiante). Em algumas outras arquiteturas, como Onion, teremos duas outras camadas. A Application Services e a Domain Services.

A Domain service encapsula regra de negócio que naturalmente não condiz mais com o domínio. Lembrando que operações CRUD não são referenciadas nessa camada. Podemos pensar na interação entre duas entidades aqui nessa camada.
A Application service é usada para fornecer meios de consumidores externos acessarem nosso sistema. Essa camada pode referenciar algum crud vindo do Repo.

Podemos enxergar essas duas camadas como composição da camada de usecases, explicada acima.

As mudanças nos usecases não devem alterar comportamento das entidades, mas a mudança nas entidades deve alterar o comportamento dos usecases.

<br/>

### Menção Onion: camada de infra
Fazendo a referência à clean, a parte de infra está presente depois da camada de usecases. A camada de infra representa toda nossa tecnologia. Tudo aquilo que não se relaciona às regras de negocio, às políticas. Aqui é a camada que criamos para mantermos comunicação com o mundo externo. Temo aqui a especificação de como é entregue (Apis, CLI, GUI), e como é feita a comunicação com terceiros (databases, filas, APIs, etc). Assim como outras configs. Em outras palavras, aqui é tudo que se relaciona com os detalhes,

### Adapters
Podemos considerar uma subcamada dentro de infra, são os adapters. Os adapters são onde definimos como nosso app vai interarir com o mundo,. Exemplos comuns podem ser APIS, gateways, endpoints RESt, handlers de message brokers, repo de databases, controllers, email services, entre outros
Podemos enxergar os adapters como translators. Eles são feitos para serem camadas "finas", contendo conversão de data (parsing, formatting, strigfication), handlers, validações de regras sintáticas e etc. Mais uma vez, não devemos colocar lógica de domínio na nossa camada de adapters

O que sobra na parte de infra é tudo que não vai na camada de adapters. Frameworks MVC, Web Frameworks, qualquer outro mecanismo que sirva como ponto para o mundo externo

### Gateways  
#### Como fica a comunicação entre usecases/services e o Repo? Interfaces
lamos que uma camada interna não pode depender de uma mais externa, certo? Devemos sempre obedecer o DIP do SOLID. Mas, e nos casos dos usecases, eles não estão dependendo dos nossos adapters? Como podemos lidar com isso?

Isso depende da linguagem, mas em Golang, podemos Definir uma interface e injetar implementações concretas. Trabalhando com injeções de dependência. Nesse caso, temos uma definição clara da comunicação com um adapter. Essa divisa de comunicação externa também é conhecido como PORT. Em termos práticos, temos uma interface. Dado que as portas abstraem adapters (que pertencem à infra), elas pertencem ao domínio.
Podemos fazer um paralelo com a arquitetura hexagonal aqui (também conhecida como ports and adapters).  Em um exemplo concreto, uma porta pode ser uma interface de service ou uma interface de um repositorio que um controller, ou até mesmo o service requer. Injetamos essa dependencia no construtor de onde queremos acessar tal implementação. Nessa arquitetura, nossos adapters dependem de uma ferramenta específica e uma porta específica, porém nossas regras de negocio só vão depender da nossa interface, feita para satisfazer nossas necessidades de negócio. 

### Presenters
Não devemos expor nossas entidades ao mundo externo. Entidades devem residir dentro do domínio. Os adapters podem chamá-las, mas para expo-las e externaliza-las (ex: JSON) usamos o conceito de PRESENTERS. Caso contrário, estaremos acoplando o domínio com o mundo externo e correndo o risco de expor informações sensíveis.


<br/>

### Conclusão (?)
Como discutido no post anterior, qualquer modelo de arquitetura existe que consideremos usar deve ser apoiado em soluções de negocio. Assim como não queremos nos prender a um framework ou banco (arquiteturalmente falando) não queremos nos prender a um modelo arquitetural, como se fosse algo escrito em pedra. É necessário observar as dificuldades do negocio, o que ele pede em si, e irmos aplicando padrões estruturados pelos demais modelos arquiteturais orientados a domínio. Os modelos nos servem como ferramenta para alcançarmos valor. É isso que se trata tudo isso, no final das contas.