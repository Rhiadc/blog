---
layout: post
title:  "OvernightJS: Uma maneira fácil de usar Express com Typescript"
date:   2021-05-22 05:24:45 -0300
categories:  Typescript Express OvernightJS
---

Decorators Typescript para Express
<img src='https://miro.medium.com/max/1522/1*qxSjwDKjdFRB1qpKV1Tilg.png'><br/>

### Introdução ao OvernightJS
O OvernightJS é uma lib que adiciona decorators Typescript ao Express. Ele não tem a premissa de adicionar novas camadas em cima do Express ou afasta-lo do modelo RESTful de se escrever APIs, diferentemente de outras libs mais complexas, o OvernightJS é relativamente simples, e bem fácil de usar pra quem já esta familiarizado com Express.<br/>

### Como ele funciona?
Depois de instalado, simplesmente importe o controller decorator, coloque ele logo antes de uma classe Typescript, e passe uma rota a ele (como uma string). Essa string será adicionada automaticamente aos métodos routes no controller. Podemos adicionar routes ao controller usando decorators para Express como GET, POST, PUT e DELETE. Os métodos funcionarão exatamente como se fossem escritas em Express puro. Os decorators suportam middlewares Express também. Assim como passaríamos o middleware como segundo argumento de uma route Express, passamos como argumento do decorator. Veja o exemplo, ao invés de fazermos isso:

{% highlight javascript %}
export class UserController {
    getRoutes(): Router {
        let router = express.Router();
        router.get('/api/users/:id', [middleware1, middleware2], (req, res, next)=>{
            this.get(req, res, next);
        })

        return router
    }

    get(req: Request, res: Response, next: NextFunction): any {
        console.log(req.params.id);
        return res.status(200).json({msg: 'get_called})
    }
}
{% endhighlight %} <br/>

fazemos o seguinte:
{% highlight javascript %}
@controller('api/users')
export class UserController {
    @Get(':id)'
    @Middleware([middleware1, middleware2])
    get(req: Requestm res: Response, next: NextFunction): any {
        console.log(req.params.id);
        return res.status(200).json({msg: 'get_called})
    }
}
{% endhighlight %} <br/>

Já podemos perceber que economizamos algumas linhas de código. E isso só tende a aumentar, assim que adicionamos novas routes.

### Instalando
{% highlight shell %}
$ npm install --save @overnightjs/core express
$ npm install --save-dev @types/express
{% endhighlight %} <br/>

### Tutorial Rápido
Após instalarmos o OvernightJS, importamos o decorator do "Controller" no começo do nosso arquivo .ts. O overnightJS também funciona com async/await. Veja mais um exemplo:

{% highlight javascript %}
import { Request, Response, NextFunction } from 'express'
import { Controller, Middleware, Get, Post, Put, Delete } from '@overnightjs/core'

@Controller('api/users')
export class UserController {
    @Get(':id')
    get(res: Request, res: Response): any {
        console.log(req.params.id);
        return res.status(200).json({msg: 'get_called'});
    }

    @Get()
    @Middleware(middleware1)
    private getAll(req: Request, Res: Response): void {
        res.status(200).json({msg: 'get_all_called'});
    }

    @Post()
    private add(req: Request, res: Response): void {
        res.status(200).json({msg: 'add_called'});
    }

    @Put('update_user')
    @Middleware([middlaware1, middleware2])
    private update(req: Request, res: Response): void {
        res.status(200).json({msg: 'update_called'});
    }

    @Delete('delete/:id')
    private delete(req: Request, res: Response): void {
        res.status(200).json({msg: 'delete_called'});
    }

    @Get('practice/async')
    private async getWithAsync(req: Request, res: Response): Promise<void> {
        let msg;
        try{
            msg = await this.algumMetodoQueRetornaPromisse(req)
        } catch (e) {
            msg = e
        } finally {
            res.status(200).json({msg: msg})
        }
    }

}

{% endhighlight %} <br/>

Assim que você configurar seu controller, importe no começo do server. A classe filha "Server", que é parte do pacote @overnightjs/core, é necessária para habilitar as rotas. Essa classe também nos provicendia uma instância Express, a qual podemos interagir normalmente. Rotas são inicializadas usando o método `addControllers()`, como pode ser visto abaixo. (Podemos passar um único controller, ou um array deles)<br/>
Outra função legal dessa lib, é que não precisamos usar o  objeto `express.Router()`. 
{% highlight javascript %}
import * as bodyParser from 'body-parser'
import { Server } from '@overnightjs/core'
import { UserController } from './UserController'
import { LoginController } from './LoginController'

export class SampleServer extends Server {
    constructor(){
        super();

        this.app.use(bodyparser.json());
        this.app.use(bodyParser.urlencoded({extended: true}));

        let loginController = new LoginController();
        let userController = new UserController();

        super.addControllers([loginController, userController]);
    }

    public start(port: number){
        this.app.listen(port, () =>{
            console.log(`server started on port ${port} `)
        })
    }
}
{% endhighlight %} <br/>

Você pode conferir mais aqui: [OvernightJS](https://www.npmjs.com/package/@overnightjs/core) 