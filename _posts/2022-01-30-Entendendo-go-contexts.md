---
layout: post
title:  "Go: Entendendo o pattern de concorrência Context"
date:   2022-02-21 13:24:45 -0300
categories: Go concorrência
---

Um contexto Go é uma ferramenta usada para compartilhamento de dados que dividem um mesmo escopo, `cancellation` e `timeout` quando trabalhamos com padrões de programação concorrente em Go.

Como descrito no overview do package, o context define um type, carrega deadlines e trabalha com cancellation signals, assim como outros valores entre limites de APIs e processos. 

Ao analisarmos o package, podemos observar que o tipo Context obedece o seguinte contrato:
```go
type Context interface {
  
  Deadline() (deadline time.Time, ok bool)
    
  Done() <-chan struct{}
      
  Err() error
        
  Value(key interface{}) interface{}
    
} 
```

Onde:
- <b>Dealine()</b>: retorna o tempo em que esse contexto será cancelado, se a ordem de cancelamento existir.
- <b>Done()</b>: retorna o channel que é fechado quando o contexto é cancelado ou é expirado.
- <b>Err()</b>: retorna a razão pelo qual um contexto foi cancelado, depois que `Done()` for fechado
- <b>Value</b>: armazena data, basicamente trabalhando como um ke-value.

Existem algums jeitos específicos de se trabalhar com contextos. Podemos propagar um contexto específico ou criar um contexto filho a partir de um específico, passando um `WithDeadline`, `WithCancel`, `WithTimeout` ou `WithValue`. Sempre que criamos um contexto filho, recebemos um novo contexto baseado no contexto oroginal. Assim, caso um contexto pai seja cancelado, todos os contextos filhos também serão cancelados. Os contextos podem ser passados para diferentes gorotinas.

Existem algumas regras específicas para a utilização de contextos, delimitados pela propria documentação. Sendo elas:
- Não usar contextos como componentes de uma struct. Ao invés disso, passar um contexto diretamente a uma função que necessite. O contexto deve ser passado como primeiro parâmetro dessa função, convencionalmente chamada de `ctx`
- Não usar contextos nil, mesmo que isso seja possível. Nesse caso, o mais correto será utilizar um `context.TODO`
- Usar valores de contextos apenas para dados de escopo de requisição que transitam entre processos e APIs.


#### Partindo agora para alguns exemplos.
Contextos com timeout são usados em sua maioria quando trabalhamos com algume requisição externa, seja uma query num banco de dados, ou alguma comunicação como HTTP, gRPC, etc.

```go
package main

import (
    "context"
    "fmt"
    "time"
)

func main() {
    // channel usado para receber o resultado da função doSomething
    ch := make(chan string)

    ctxTimeout, cancel := context.WithTimeout(context.Background(), time.Second*3)

    defer cancel()

    go doSomething(ctxTimeout, 1, ch)

    select {
    // esperamos para checar se um contexto expirou (3 segundos)
    case <- ctxTimeout.Done():
        fmt.Printf("Context cancelado: %v\n", ctxTimeout.Err())
    
    //esperamos para checar se recebemos o resultado de doSomething
    case result := <- ch:
        fmt.Printf("doSomething finalizado: %v\n", result)
    }
}

func doSomething(ctx context.Context, timeSleep time.Duration, ch chan string){
    fmt.Println("Sleeping...")
    time.Sleep(time.Second * timeSleep)
    fmt.Println("Waking up...")
    ch <- "Did something"
}
```

O que estamos fazendo aqui?

- Criamos a função DoSomething, que recebe um contexto, um tempo que ficara inativa, e um channel para enviar o resultado "Did something"
- Criamos um context e criamos um filho a partir dele, com um timeout de 3 segundos.
- Chamamos doSomething, passando 1 segundo de duração. Aqui queremos que a função espere 1 segundo antes de enviar os resultados ao channel
- No `select`, temos um caso de verificação para saber se o contexto deu timeout (com o Done())
- Ainda no `select`, estamos esperando receber alguma coisa no channel (quando doSomething finalizar)
- Ao usarmos um `defer cancel()` estamos dando uma ordem para que a função cancel seja invocada na finalização da função. Assim, o context será cancelado

Ao chamarmos nossa função, o resultado será: <br/>
<img src="https://imgur.com/nro2v2sl.png"  style="display: block; margin: 0 auto;">

Tudo funcionou como o esperado. Mas e fizessemos a função doSomething dormir por 5 segundos?

<img src="https://imgur.com/boogdD0l.png"  style="display: block; margin: 0 auto;">

Como desejado, nosso contexto foi expirado.

#### Compartilhando dados entre goroutines.
Contextos `withValue` são mais usados entre middlewares HTTP. Quando queremos compartilhar algum dado entre uma pipeline de requisições. Um caso de uso poderia ser um middleware que faz o parse de um token JWT, cria um contexto para armazenar esse dado. Dai um outro middleware verifica alguma permissão relacionado ao dado armazenado nesse contexto. Conseguimos realizar tais compartilhamento de dados com os nossos contextos. 

Por exemplo:

```go
package main

import (
    "net/http"
    "context"
)
    func addContextValueMiddleware(next http.Handler) http.Handler {
        return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
            ctx := context.WithValue(r.Context(), "Name", "Somename")
            r = r.WithContext(ctx)
            next.ServeHTTP(w, r)
        })
    }
```

Criamos um middleware HTTP que cria um contexto `withValue`. Lembrando que esse contexto é proveniente de um contexto pai. Alteramos o contexto original da request para utilizarmos o contexto que acabamos de criar (que contem o valor name "Somename")


```go
package main

import (
    "net/http"
    "fmt"
)

func main() { 
    finalHandler := http.HandlerFunc(testHandler)
    http.Handle("/teste", addContextValueMiddleware(finalHandler))

    http.ListenAndServe(":8080", nil)
}

func testHandler(w http.ResponseWriter, req *http.Request) {
    fmt.Println(req.Context().Value("Name"))
}
```
Nesse cenário, conseguimos acessar o valor do contexto passado no middleware.

<br/>

#### Cancellation signal
Cancellations signal são mais utilizados para prevenir um trabalho desnecessário. Conseguimos cancelar todas as nossas Goroutinas que compartilham do mesmo contexto. Vejamos outro exemplo:

```go
func main() {
    ctx, cancel := context.WithCancel(context.Background())
    defer cancel()

    go func() { 
        time.Sleep(time.Second * 5)
        cancel()
    }()

    go doSomething(ctx, 3)
    go doAnotherThing(ctx, 8)

    fmt.Scanln()
}

func doSomething(ctx context.Context, timeSleep time.Duration) {
    fmt.Println("starting doSomething")
    select { 
    case <- time.After(time.Second * timeSleep):
        fmt.Println("Finalizando doSomething")
    case <- ctx.Done():
        fmt.Println("Cancelando doSomething")
    }
}

func doAnotherThing(ctx context.Context, iterations int) {
    for i:=0; i<iterations; i++{
        if ctx.Err() := nil {
            fmt.Println("Abandonando função...")
            return
        }
        time.Sleep(time.Second * 1)
        fmt.Println("Iteration ", i)
    }
}
```

Explicando o código:
- Criamos um context com um `WithCancel`, assim, quando a função `cancel` for chamada, o contexto será cancelado.
- Criamos uma goroutine que chamará a função `cancel` depois de 5 segundos
- A função doSomething demorará 3 segundos para processar. Aqui estamos usando um `select`. Ou identificamos um sinal de cancelamento, ou a função finalizará após 3 segundos.
- Também criamos a função doAnotherThing, que fará 8 iterações, esperando 1 segunda para cada iteração. A cada iteração, estamos verificando o signal a cada iteração.

Nesse cenário, como cancelamos o contexto após 5 segundos, a função `doSomething` teve tempo de acabar, mas a `doAnotherThing` não. Caso cancelassemos o context após 1 segundo, nenhuma das duas funções seria finalizada. Caso cancelássemos com 10 segundos, as duas seriam finalizadas.