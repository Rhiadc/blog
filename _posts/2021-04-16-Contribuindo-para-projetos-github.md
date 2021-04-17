---
layout: post
title:  "Contribuindo para um projeto open source. Primeiros passsos"
date:   2021-04-16 23:24:45 -0300
categories:  Github
---


### Forke o repositorio
O primeiro passo é forkar o repositório em qual irá trabalhar na issue. Nesse exemplo, forkarei o repo `first-contributions` <br/><br/>
### Clone o repositório
Após isso, clone o repositório que você acabou de forkar. 

{% highlight shell%}
git clone https://github.com/seu_github/first-contributions.git
{% endhighlight %}<br/>
### Crie uma branch
Entre na pasta do repositorio que acabou de clonar
{% highlight shell%}
cd first-contributions
{% endhighlight %}<br/>

Agora crie uma branch usando `git checkout`
{% highlight shell%}
git checkout -b your-new-branch-name
{% endhighlight %}<br/>
### Fazendo as modificações necessárias e commitando
adicionando o arquivo modificado
{% highlight shell%}
git add arquivoModificado.js
{% endhighlight %}<br/>

Agora commite a modificação, adicionando uma mensagem

{% highlight shell%}

git commit -m "arquivoModificado.js modificado"
{% endhighlight %}<br/><br/>


### Push
De um push nas mudanças usando o comando `git push`

{% highlight shell%}
git push origin your-new-branch-name
{% endhighlight %}<br/><br/>

### Enviando as mudanças para review
Em seu repo, você irá ver um botão `Compare & pull request`. Clique nele.
Deixe um comentário sobre o pull e espere o `merge` ser aceito.<br/><br/>

E é isso, acabamos de completar um padrão `fork > clone > edit > pull` <br/>

