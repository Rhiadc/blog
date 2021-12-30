---
layout: post
title:  "Testes e Testes Table Driven em Go"
date:   2021-12-28 13:24:45 -0300
categories: Go Testes TDD
---

Em Go, testes de unidade funcionam como funções Go normais (embora obedeçam algumas regras em específico). Aqui está um exemplo de um teste padrão em go. Por exemplo, tomemos o seguinte arquivo:
`example.go`

```go
package sum

func Sum(numbers []int) int {
	sum := 0
	for _, v := range numbers {
		sum += v
	}
	return sum
}

func SumAll(numToSum ...[]int) []int {
	sum := []int{}
	for _, v := range numToSum {
		if len(v) > 0 {
			sum = append(sum, Sum(v))
		} else {
			sum = append(sum, 0)
		}
	}

	return sum
}
```

para testarmos essas funções, criamos o arquivo `example_test.go`:

```go
package sum

import (
	"reflect"
	"testing"
)


func TestSum(t *testing.T) {
	numbers := []int{1, 2, 3, 4, 5}

	got := Sum(numbers)
	want := 15

	if got != want {
		t.Errorf("got %d want %d given, %v", got, want, numbers)
	}
}

func TestSumAll(t *testing.T) {

	checkSum := func(t testing.TB, got, want []int) {
		t.Helper()
		if !reflect.DeepEqual(got, want) {
			t.Errorf("got %v want %v", got, want)
		}
	}

	t.Run("Make sums of tails of", func(t *testing.T) {
		got := SumAll([]int{1, 2}, []int{0, 9})
		want := []int{3, 9}
		checkSum(t, got, want)
	})

	t.Run("Safely sum empty slices", func(t *testing.T) {
		got := SumAll([]int{}, []int{0, 9})
		want := []int{0, 9}
		checkSum(t, got, want)
	})

}
```

Daqui podemos tirar alguns aspectos importantes, sendo eles:
- o arquivo de teste tem o mesmo nome do arquivo a ser testado, com o sufixo `_test` no final, nesse caso: `example_test.go`.
- O nome da função de teste começa com o prefixo `Test`, nesse caso `TestSum` e `TestSumAll`.
- O função de teste recebe um argumento do tipo `*testing.T`, que é um tipo injetado pelo package de teste. 
- Na função de teste `TestSum`, temos uma simples verificação se o valor passado à função `Sum` é o mesmo valor passado no teste (want).
- Já na função `TestSumAll`, temos dois subtestes descritos. Nesse caso, rodamos dois casos diferentes (com diferentes descrições) e chamamos a `checkSum` em ambas para validarmos o teste.


<br/>

### Alguns comandos úteis:
 para rodarmos os nossos testes: `go test` <br/>
```
$ go test ./...
```

 O resultado é o seguinte:
```
ok      github.com/exusr/gotests       0.002s
```
<br/>

Em alguns casos, queremos ver exatamente os testes que estão rodando, e quanto tempo eles demoram pra rodar, nesse caso: `go test -v`

```
$ go test -v
```

```
=== RUN   TestSum
--- PASS: TestSum (0.00s)
=== RUN   TestSumAll
=== RUN   TestSumAll/Make_sums_of_tails_of
=== RUN   TestSumAll/Safely_sum_empty_slices
--- PASS: TestSumAll (0.00s)
    --- PASS: TestSumAll/Make_sums_of_tails_of (0.00s)
    --- PASS: TestSumAll/Safely_sum_empty_slices (0.00s)
```

<br/>

Em outros casos, queremos ver a porcentagem de coverage. Isso é útil para sabermos a porcentagem de testes que temos em nosso códig. O comando é: `go test -cover`
```
$ go test -cover
```

```
ok     github.com/exusr/gotests  0.002s  coverage: 100.0% of statements
```

<br/>

Podemos criar uma visualização gráfica de qual parte do codigo ainda não testamos. Isso ajuda a encontramos lacunas em nosso coverage e como corrigi-las. Alcançamos isso com os comandos `go test`, que irá gerar um `coverprofile`. que posteriormente converteremos em HTML com o `go tool cover`.

```
 $ go test -coverprofile=coverage.out 
```

```
PASS
coverage: 100.0% of statements
ok      github.com/exusr/gotests       0.004s
```
E ai geramos o HTML com o comando
```
$ go tool cover -html=coverage.out
```

e aí, teremos:
![coverage](https://imgur.com/C2k7KyY.png)

<br/>

### Usando Table driven tests

No exemplo acima, precisamos escrever código duplicado para os diferentes casos de testes que queremos realizar. Além disso, alguns casos podem ser pouco intuitivos e chatos de se escrever. E se pudessemos escrever um tipo de teste que englobasse todos os nossos casos, fosse mais intuitivo de se escrever e requeresse poucas mudanças em novos casos de testes? Bem, é disso que se trata o table driven test. Veja o exemplo do seguinte caso.

<br/>

Ainda no arquivo `example.go`

```go
type StatusID uint

const (
	StatusProcessing StatusID = 1
	StatusDenied     StatusID = 2
	StatusCompleted  StatusID = 3
)

var SomeStatuses = map[StatusID]string{
	StatusProcessing: "Processing",
	StatusDenied:     "Denied",
	StatusCompleted:  "Completed",
}

type Product struct {
	StatusID StatusID
}

func (p *Product) StatusName() string {
	return SomeStatuses[p.StatusID]
}

```
Explicando o exemplo acima, temos um mapa de keys do tipo `StatusID` e valor do tipo string, nos valores `Processing`, `Denied` e `Completed`. Temos também um metodo `StatusName` referente à Struct `Product` que possui o campo `StatusID`, do tipo `StatusID`. Nesse caso, queremos testar se os casos referentes aos IDs passados serão os mesmos declarados no nosso mapa. Veja o teste:

```go
func TestStatus(t *testing.T) {
	z := []struct {
		name    string
		product Product
		want    string
	}{
		{
			name:    "should Return Processing",
			product: Product{StatusID: StatusProcessing},
			want:    "Processing",
		},
		{
			name:    "should Return Denied",
			product: Product{StatusID: StatusDenied},
			want:    "Denied",
		},
		{
			name:    "should Return Completed",
			product: Product{StatusID: StatusCompleted},
			want:    "Completed",
		},
	}
	for _, tt := range z {
		t.Run(tt.name, func(t *testing.T) {
			if got := tt.product.StatusName(); got != tt.want {
				t.Errorf("Product.StatusName() = %v, want %v", got, tt.want)
			}
		})
	}
}
```

Explicando o caso acima:
- Definimos a struct de referencia com os campos:
  - `name`: será o nome (ou descrição) do teste, será usado na hora de exibir a falha.
  - `product`: Do tipo `Product`, sera usado como a struct que estamos testando.
  - `want`: É usado como referência de caso de sucesso.
- Para cada caso de testes, adicionamos uma struct ao slice.
- Caso quisessemos adicionar mais um caso de teste, era só adicionar mais uma struct com seus respectivos valores relacionados ao caso.
- Na hora de testar, usamos um simples `for` iterando sobre o slice de struct.

<br/>
Nesse caso, podemos perceber que a criação de novos casos ficou muito mais intuitiva, e, dependendo da quantidade de casos de testes, economizaremos muitas linhas de código.