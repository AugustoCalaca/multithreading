## Introdução
Códigos são frequentemente escritos da forma serializada. Ou seja, o código é execultado sequencialmente, uma linha após a outra de maneira monolítica, sem considerar a possibilidade de mais processadores disponíveis que o programa poderia explorar.

Com o aumento da popularidade de máquinas com multiprocessamento simétrico (em grande parte devido aos processadores multicores), programação com threads é um conjunto de habilidades valiosas que vale a pena aprender.

Por que muitos programas são sequenciais? Uma hipótese seria que os alunos não são ensinados como um programa em paralelo funciona no tempo ideal. Para piorar, códigos multithread são difíceis e não-triviais. Cuidado na análise do problema, e então um bom projeto para programação multithread não é uma opção; é uma nececssidade absoluta.


## O que é uma thread?
Para definir uma thread, primeiro deve-se entender os seus limites de operação. Um programa se torna um processo quando ele é carregado de um espaço reservado na memória do computador e entra em execução. Um processo pode ser executado por um processador ou por um conjunto deles. A descrição de um processo na memória contêm informações vitais tais como: a instrução atual que é executada, registros, variáveis armazenadas, arquivos manipulados, sinais e etc.

A thread é uma sequencia de instruções dentro de um programa que pode ser executada independentemente de outro código. Ela está dentro do mesmo espaço de endereçamento de um processo, assim muitas das informações presentes na memória descritas pelo processo podem ser compartilhadas através das próprias threads. 

Algumas informações não podem ser replicadas tais como a pilha (ponteiro da pilha aponta para uma área de memória diferente por thread), registros e dados específicos da thread. Estas informaçãoes permitem a thread trabalhar independentemente da thread principal do programa e possivelmente das outras threads.

Felizmente, os principais sistemas operacionais modernos susportam threads. Cada qual podendo usar diferentes mecanismos para implementá-lo.

## Alguns conceitos
* Processo leve (Lightwight Process-LWP). Pode ser pensado uma CPU virtual onde o número de LWPs é geralmente maior que o número de CPUs do sistema.
* Modelo X-to-Y. Mapeamento entre LWPs e threads.
* Contenção de escopo é como as threads competem por recursos do sistema.
* Threads encadeadas. Têm escopo de contenção em todo o sistema, em outras palavras, essas threads lidam com outros processos em todo o sistema.
* Threads não-encadeadas. Tem escopo de contenção de processos.
* Thread segura. Signfica que o programa protege os dados compartilhados.
* Reetrant. Significa que o programa pode ter mais que uma thread executando simultaneamente.
* Segurança assincrona. Significa que uma função é reetrant ao manipular um sinal.
* Concorrência vs. Paralelismo - São coisas diferentes! Paralelismo implica na execução simultânea de código (o que não é possível em máquinas com um único processador), enquanto concorrência implica que muitas tarefas podem ser executadas em qualquer ordem e possivelmente em paralelo.


## Padrões de projeto Thread
* Thread pool (Boss/Worker). Uma thread envia outras threads para fazer o trabalho útil que geralmente fazem parte de um pool de trabalho da thread. Nesta discussão o pool é geralmente pré-alocado antes que o boss (chefe) comece a distribuir as threads para o trabalho.
* Peer. O modelo peer é semelhante ao modelo boss/worker, exceto quando o pool de trabalho é criado, o boss torna-se outra thread no thread pool e, portanto, é um peer para as outras threads.
* Pipeline.  Cada thread é parte de uma longa cadeia em uma fábrica de processamento. Cada uma trabalha em dados processados pela thread anterior e passa para a próxima thread. Deve-se ter cuidado para distribuir igualmente o trabalho e tomar medidas extras para garantir o comportamento de não bloqueio neste modelo.

## Protegendo recursos compartilhados
As threads devem operar em dados diferentes, mas geralmente elas podem acessar os mesmos dados. Não é seguro permitir acesso simultâneo a esses dados ou recursos sem algum mecanismo que defina um protocolo de acesso seguro. Elas devem ser explicitamente intruídas para bloquear quando outras threads tentarem acessar os mesmos recursos.
### Exclusão mútua
A exclusão mútua é o método de serializar o acesso a recursos compartilhados. Você não quer que uma thread modifique uma variável que já está sendo modificada por outra thread! Outro cenário é uma leitura suja onde o valor está sendo atualizado e outra thread lê o valor antigo. O protocolo usado é o **Mutex** que é um bloqueio que pode ser anexado virtualmente a algum recurso.
Como analogia, você pode pensar no **mutex** como um cofre com apenas uma chave (no caso do mutex padrão), e o recurso é proteger o coteúdo de dentro do cofre. Apenas uma pessoa pode ter a chave no peito a qualquer momento, portanto, é a única pessoa autorizada a olhar ou modificar o conteúdo do baú no momento em que ele possui a chave.

### Tipos de Mutex
Existem diferentes tipos de bloqueios além do tipo de bloqueio padrão simples.
* Recursivo: permite que uma thread mantenha o bloqueio para adquirir o mesmo bloqueio novamente, o que pode ser necessário para algoritmos recursivos.
* Queuing (Enfileiramento): permite a imparcialidade na aquisição de bloqueio fornecendo pedidos FIFO (first in first out) para a chegada de solicitações. Essas exclusões mútuas podem ser mais lentas devido ao aumento da sobrecarga e à possibilidade de ter que ativar threads em seguido plano que pode estar dormindo.
* Reader/Writer (Leitor/Gravador): permite que vários readers adquiram o bloqueio simultaneamente. Se os readers existentes tiverem o bloqueio, um pedido de writer será bloqueado até que todos os readers tenham desistido do bloqueio. Isso pode levar ao escritor fome.



