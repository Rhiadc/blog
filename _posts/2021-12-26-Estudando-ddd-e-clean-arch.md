---
layout: post
title:  "Estudando arquitetura de software com DDD e Clean em Go"
date:   2021-12-26 13:24:45 -0300
categories: Go Arquitetura
---

Ao estudarmos os diferentes tipos de arquiteturas como **Clean Architecture**, **Onion**, **Hexagonal**, ou a conceitos arquiteturais aplicados ao **DDD**, é comum confundirmos ou até mesmo trabalharmos com ideias semelhantes (pertencentes às diferentes arquiteturas) aplicadas de diferentes maneiras.

Nesse artigo, pretendo esclarecer e estudar conceitos de Clean e Hexagonal, assim como passar a minha visão acerca dessas implementações.
<br/>

### Enxergando nossa aplicação como átomo 
É comum vermos a nossa aplicação como um condensado de várias camadas, assim como uma estrutura atômica, onde o domínio é o núcleo, e as demais camadas são os orbitais, veja: <br/>
<img src="https://images.ctfassets.net/1es3ne0caaid/7cxBEVeTfy8syMeiuoOKK8/068001323f4dfecbf26036eff0b8424c/clean-architecture-ex-3.png" style="display: block; margin: 0 auto;">


### Domain
Domínio, também conhecido como **Núcleo** ou **Core** é o lugar onde reside toda lógica de negocio e modelo de domínio da nossa aplicação, contendo toda a identidade da mesma. O núcleo deve ser independente de qualquer tecnologia, tipo DB, Frameworks, Aplicações de mensageria, etc. 
Podemos enxergar o domínio como o "espirito" da nossa aplicação. Ou seja, caso alterassemos o nosso domínio, estariamos alterando diretamente a essência da nossa aplicação. Falaremos sobre o domínio logo mais nesse post.


### Infrastructure
A camada de insfraestrutura será responsável por acessar serviços externos que o Domínio não tem acesso, como por exemplo, DB, serviços de mensagem, email, etc. Em outras palavras, a camada de Infra representa a tecnologia, e tudo aquilo que não se refere ao domínio da nossa aplicação, determinando assim como o core da aplicação se comunica com o "mundo externo".
<br/><br/>

#### Estado: Domínio x Infra
Consideramos o domínio como núcleo da aplicação pois ele se comporta de maneira estável, isso é, de maneira menos propensa a sofrer modificações e menos sensível às tecnologias. Já a camada de infra apresenta uma volatilidade maior. Como está diretamente ligada às tecnologias, é natural que venha sofrer várias modificações durante o andar do desenvolvimento. Um dos objetivos da arquitetura **domain driven** (ou orientada a Domínio) é proteger e isolar o domínio, fazendo assim com que este não "se importe" com o que não lhe diz respeito.
<br/><br/>

### Aprofundando nas camadas
#### Infra: Adapters
A camada **Adapter** é uma subcamada de infra. os **adapters** podem ser considerados "tradutores" de comunicação. É nessa camada que convertemos dados nos formatos mais convenitentes, partindo do nosso domínio, com destino "mundo externo". Essa camada deve apenas apresentar *translators*, *parsers*, *formatters*, *handlers*, *validators*, etc. <br/>
![adapters](https://miro.medium.com/max/913/1*vPsfoD9O8zxyptYwSHl2Ag.png)
- A camada de Adapter não deve conter logica relacionada ao negócio.
- Em alguns casos não será possível construir um adapter totalmente agnostico, embora seja uma boa prática construir um o mais agnostico possível. 
<br/><br/>

#### Domain: Entidades
A camada de **Entidade** consiste de funções que implementam regras críticas de negócio em mais baixo nível. Essa camada contém logica direcionada a sua menor unidade de domínio, por exemplo: 
- Accounts
- Movies
- Transactions

É importante lembrar também que as entidades de domínio não tem ligação com entidades de banco, não mantendo nenhum contato com eles.
<br/><br/>

#### Domain: services
Nas camadas do DDD (implementadas em um modelo de arquitetura Onion), a camada de **Domain Service** é uma camada que se liga ao domínio. É uma camada utilizada quando queremos criar uma lógica que depende de mais de 1 entidade do nosso domínio.

 <img src="https://miro.medium.com/max/913/0*Ahy_hXrFysq-_j00.png" width="300" height="300" style="display: block; margin: 0 auto;">

<br/><br/>
Criamos essa camada quando o fato de criarmos uma camada de entidade quebraria nosso encapsulamento. 

Por exemplo: 
- Caso quisessemos criar a função 
```go
func UpvotePost(member domain.Member, post domain.Post) {
    ...
}
```
Onde a criariamos? A qual entidade tal função deveria pertencer? `Member`? `Post`? Ambas?
E aí que implementamos a nossa camada **Domain Service**, nesse caso, `post_service.go`
<br/><br/>

### Use Cases
É aqui que as coisas começam a complicar um pouco. Tanto no DDD quanto na Clean Architecture está presente como subcamada da camada de aplicação. Mas digamos que ela se relacione ao domínio, como na seguinte figura:

![use-cases](https://miro.medium.com/max/913/1*hVrM4LymfJQVRC0vtG5Gvg.png)

Em vias gerais, a camada de **Use case**
- não contém lógica de apenas um domínio em específico
- É usada para dar **fetch** em entidades específicas de domínio em camada de persistência, mantendo contato com os serviços de domínio.

Exemplo de aplicação da função `UpvotePost` em `usecases/upvote_usecase.go`

- Busca `member` no Repo
- Busca `Post`no Repo
- Chama o método `UpvotePost` no `post_service.go`
- Efetua a transação
- etc

Ou seja, a camada de Use case é responsável por funcionar como intermediária entre os serviços, repositorios, etc. criando assim um caso de uso pertinente à nossa aplicação.
<br/><br/>

### Portas e adaptadores
De acordo com o princípio da inversão da dependência (o DIP no SOLID), camadas mais internas não devem depender das camadas mais externas da arquitetura.
Em outras palavras, nosso **domínio** não pode depender de qualquer **adapter**, etc. Entretanto, nossa camada de **Use case** se comunica com a de banco, entre outros adaptadores mais externos. 
Para resolver esse problema, definimos uma **interface** e injetamos implementações concretas em nossos `Use Cases`. Chamamos essa resolução de **Portas**. As portas são as responsáveis por fazer a intermediação entre comunicadores externos e fornecer os dados demandados pelo domínio.

Exemplo de interface:
```go
type PostRepository interface {
	CreatePost(p *models.Post) error
	GetPost(Id int) (models.Post, error)
	GetPosts() []*models.Post
	...
}
```

pra finalizar, um exemplo de raiz de uma aplicação que utiliza conceitos de Clean, hexagonal e DDD:

```
bank_service/
├─ infra/
│  ├─ adapters/
│  │  ├─ gorm/
│  │  │  ├─ models.go
│  │  │  ├─ repo.go
│  │  │  ├─ seeds.go
│  │  │  ├─ migrations.go
│  │  │  ├─ config.go
│  │  ├─ kafka/
│  │  │  ├─ etc/
│  │  │  ├─ kafka.go
│  │  │  ├─ logable_kafka.go
│  │  │  ├─ message_publisher.go
│  │  ├─ redis/
│  │  │  ├─ client.go
│  │  │  ├─ config.go
├─ domain/
│  ├─ events/
│  │  ├─ event.go
│  │  ├─ services/
│  │  │  ├─ event_service.go
│  ├─ user/
│  │  ├─ services/
│  │  │  ├─ user_service.go
│  │  ├─ user.go
│  ├─ account/
│  │  ├─ services/
│  │  │  ├─ account_service.go
│  │  ├─ account.go
│  ├─ transaction/
│  │  ├─ services/
│  │  │  ├─ transaction_status.go
│  │  │  ├─ transaction_history.go
│  │  │  ├─ transaction_event.go
│  │  ├─ transaction.go
├─ usecases/
│  ├─ create_user/
│  │  ├─ create_user_usecase.go
│  ├─ create_account/
│  │  ├─ create_account_usecase.go
│  ├─ transactions/
│  │  ├─ create_transaction_usecase.go
│  │  ├─ register_transaction_usecase.go
├─ main.go

```

Referências:
- https://medium.com/codex/clean-architecture-for-dummies-df6561d42c94
- https://www.yiiacademy.com.br/2021/04/11/5-clean-architecture-infra-layer-adapters/
- https://khalilstemmler.com/articles/software-design-architecture/domain-driven-design-vs-clean-architecture/
- https://khalilstemmler.com/articles/typescript-domain-driven-design/entities/