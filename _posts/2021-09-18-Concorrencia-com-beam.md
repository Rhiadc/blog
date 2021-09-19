---
layout: post
title:  "Concorrência com a BEAM"
date:   2021-09-18 13:24:45 -0300
categories: Elixir BEAM 
---

### Motivação

É extremamente ruim que um request bloqueado acabe bloqueando todos os outros requests. Fila de bloqueio = Irresponsividade. <br/>
Num server típico, temos que lidar com requests simultâneos, manter um estado compartilhado (cache, session, server data, etc), o que nos faz querermos rodar todas as tarefas simultaneamente de modo rápido e eficiente. Neste caso, muitas tarefas ainda estão pendentes, e queremos efetua-las em paralelo o máximo possível. <br/>
É exatamente o que a <strong>BEAM</strong> faz pra gente. Em <strong>BEAM</strong>, um processo é uma thread de execução concorrente. Eles rodam concorrentemente e podem rodar em paralelo caso +1 core esteja disponível. Processos BEAM utilizam o princípio de schedulers de sua VM para gerenciar a execução concorrente. A VM BEAM utiliza o tanto de schedulers quanto há CPUs disponíveis, por exemplo. Num quad core, quatro schedulers serão utilizados. <br/>
O scheduler é responsável pela alternância entre processos. Cada processo tem um timeslot, depois que esse tempo acaba, ele é interrompido preemptivamente (multitask preemptivo); Processos são leves, levando poucos microssegundos para serem criados. Seus footprints de memoria têm apenas alguns kbs (enquanto processos de sistema operacional usam alguns mbs apenas para stack). O número máximo de processos que teoricamente podem ser criados pela VM BEAM é de 134kk. <br/>
Usar um processo para cada task é um ótimo meio de paralelizar a execução (em várias cpus), iniciar um processo para cada task significa <strong>isolamento</strong>, e isso significa não compartilhamento de memória, tolerância a falha, escalabilidade (mais vantágem de processamento com mais hardware) e facilidades no gerenciamento da nossa aplicação.
