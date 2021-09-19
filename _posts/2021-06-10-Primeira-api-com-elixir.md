---
layout: post
title:  "Conhecendo Phoenix: primeira API"
date:   2021-06-10 01:24:45 -0300
categories:  Ecto Elixir Phoenix
---

### Inicializando nosso projeto.
Primeiro, rodaremos o seguinte comando para gerarmos um novo projeto Phoenix sem HTML, visto que nosso projeto não deverá renderizar conteúdos estáticos.
{% highlight shell %}
mix phx.new busi_api --no-html
cd busi_api
{% endhighlight %}

#### Configurando nossa conexão ao banco de dados
{% highlight elixir %}
config: busi_api, BusiApi.Repo,
    adapter: Ecto.Adapters.Postgres,
    username: "postgres",
    password: "postgres",
    database: "busi_api_dev",
    hostname: "localhost",
    pool_size: 10
{% endhighlight %}<br/>
Aqui, configuramos o username, password e hostname com os nomes padrões do Postgres. Voltando ao terminal, vamos rodar os seguintes comandos para criar nossa DB e iniciar o servidor.<br/>
{% highlight shell %}
mix ecto.create
mix phx.server
{% endhighlight %}<br/>
Veremos o erro `no route found for GET / (BusiApiWeb.Router)`, isso é porque ainda não temos nenhuma rota configurada. No nosso router.ex, vamos adicionar a seguinte parte de código, e depois criar o DefaultController

{% highlight elixir %}
#/lib/busi_api_web/router.ex

scope "/" do
      pipe_through [:fetch_session, :protect_from_forgery]
      live_dashboard "/dashboard", metrics: BusiApiWeb.Telemetry
    end

#/lib/busi_api_web/controllers/default_controller.ex:

defmodule BusiApiWeb.DefaultController do
    use BusiApiWeb, :controller

    def index(conn, _params) do
        text conn, "BusiApi!"
    end

end
{% endhighlight %}<br/>

Ao recarregarmos a página, deveremos ver o texto `"BusiApi!"` na tela.<br/><br/>


### Criando nossa primeira API JSON

Para criarmos nosso model da API JSON, rodaremos o seguinte comando:
{% highlight shell %}
mix phx.gen.json Directory Business businesses name:string description:text tag:string
{% endhighlight %}<br/>

Esse comando criará uma série de arquivos, incluindo um controller, as migrations e os arguivos de teste. Após rodarmos esse comando, o Phoenix pedirá para adicionarmos o resource na nossa <strong>lib/busi_api_web/router.ex</strong> e rodaremos o comando <strong>mix ecto.migrate</strong><br/>
{% highlight elixir %}
scope "/api", BusiApiWeb do
    pipe_through :api
    resources "/businesses", BusinessController, except: [:new, :edit]
end
{% endhighlight %}<br/>

Antes de testarmos nossa API, vamos adicionar alguns valores iniciais. Abra <strong>priv/repo/seeds.exs</strong> e adicione os seguintes valores:<br/>
{% highlight elixir %}
alias BusiApi.Repo
alias BusiApi.Directory.Business

Repo.insert! %Business{name: "Company 1", description: "short description...", tag: "IT, software"}

Repo.insert! %Business{name: "Company 2", description: "Short description...", tag: "Marketing"}

Repo.insert! %Business{name: "Company 3", description: "Short description...", tag: "Accounting"}

{% endhighlight %}<br/>
Agora rodamos o comando <strong>mix run priv/repo/seeds.exs</strong> para adicionarmos os valores à tabela. Restarte o server para vermos os registros JSON. Nós também podemos testar nossa API com o postman fazendo uma requisição do tipo `POST` para a url: `http://localhost:4000/api/businesses`

{% highlight javascript %}

{
    “business”: 
        {
            “name”: “Yet another company”, 
            “description”: “Another short description!”, 
            “tag”: “IT”
        }
}
{% endhighlight %} <br/>

Vamos começar com nosso <strong>BusinessController</strong>, nesse caso <strong>list_businesses()</strong> retorna todos os registros.<br/>
{% highlight elixir %}

def list_businesses do
    Repo.all(Business)
end
{% endhighlight %} <br/><br/>

Na segunda parte, criaremos um sistema de autenticação com Guardian and Comeonin.