---
layout: post
title:  "Concorrência com a BEAM"
date:   2021-09-18 13:24:45 -0300
categories: Elixir BEAM 
---


### Motivação

É extremamente ruim que um request bloqueado acabe bloqueando todos os outros requests. Fila de bloqueio = Irresponsividade. <br/>
Num server típico, temos que lidar com requests simultâneos, manter um estado compartilhado (cache, session, server data, etc), o que nos faz querermos rodar todas as tarefas simultaneamente de modo rápido e eficiente. Neste caso, muitas tarefas ainda estão pendentes, e queremos efetua-las em paralelo o máximo possível. <br/>



### Concorrência em BEAM 

É exatamente o que a <strong>BEAM</strong> faz pra gente. Em <strong>BEAM</strong>, um processo é uma thread de execução concorrente. Eles rodam concorrentemente e podem rodar em paralelo caso +1 core esteja disponível. Processos BEAM utilizam o princípio de schedulers de sua VM para gerenciar a execução concorrente. A VM BEAM utiliza o tanto de schedulers quanto há CPUs disponíveis, por exemplo. Num quad core, quatro schedulers serão utilizados. (1 scheduler em User-land para cada thread real). As funções em Erlang não são só fibers, elas são corotinas, e a VM BEAM tem o poder de arbitrariamente pausar uma função quando bem entender, ou seja, não depende de yield para alternar controle. Exatamente como o Kernel do Linux faz. Ou seja. o Erlang implementa o equivalente a um scheduler preemptivo de green threads (thread que só existe em user-land, e não em kernel-space) em user-land, e ao invés de fibers, com corotinas verdadeiras<br/><br/>

![This is a alt text.](https://miro.medium.com/max/1400/0*acC0P3hFGAGywgGz.png "This is a sample image.")

<br/><br/>

O scheduler é responsável pela alternância entre processos Erlang (corotinas). Cada processo tem um timeslot, depois que esse tempo acaba, ele é interrompido preemptivamente (multitask preemptivo); Processos são leves (2KB de alocamento), levando poucos microssegundos para serem criados. Seus footprints de memoria têm apenas alguns KBs (enquanto processos reais de sistema operacional usam alguns MBs apenas para stack). O número máximo de processos que teoricamente podem ser criados pela VM BEAM é de 134kk. <br/><br/>


### Caracteristicas
Usar um processo para cada task é um ótimo meio de paralelizar a execução (em várias cpus), iniciar um processo para cada task significa <strong>isolamento</strong>, e isso significa não compartilhamento de memória entre processos (embora um mailbox interprocessos para comunicação), tolerância a falha (cada processo tem um garbage-collector próprio, ou seja, um processo que falha não corrompe o sistema), escalabilidade (mais vantágem de processamento com mais hardware) e facilidades no gerenciamento da nossa aplicação. Podemos citar também o importante papel dos Supervisors. Quando subimos nossa aplicação no boot da VM, subimos também um supervisor, que iniciará os processos e garantirá que caso algum dele crasheie, ele subirá um novo no lugar. Assim, podemos imaginar o Erlang como um Java ou .NET, mas que nos oferecem dois serviços característicos e essenciais que as duas outras não têm: <strong>Schedulers</strong> e <strong>Processos Erlang (green threads)</strong>. Tudo rodando em user-land. Tornando muito barato todo esse processo. (10k processos Erlang consomem 19MB). Green threads são ordens de grandeza mais baratas que uma thread real, e se a propria VM tem seu scheduler de thread real para rodarmos green threads, temos paralelismo real.<br/><br/>