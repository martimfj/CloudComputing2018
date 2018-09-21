# Cap. 2 - Deployment Orchestration - 3 Aulas
### Hugo Mendes e Martim José
## Instalando Juju:
Juju é uma ferramenta opensource de modelagem de aplicações, que permite implantar, configurar, dimensionar e operar infraestruturas em nuvem de maneira rápida e eficiente, seja em núvens públicas como privadas. É possível várias soluções por meio de simples comandos, como por exemplo instalar o wordpress (`juju deploy wordpress`).

Ao instalar o Dashboard de administração, nos deparamos com um erro na máquina, impossibilitando que a instalação fosse concluída. Para consertar isso, acessamos a máquina via ssh por meio do comando `juju ssh 0/lxd/0` e por meio do comando `sudo pip install six --ignore-installed` instalamos o pacote faltante sem verificar os outros necessários/pendentes.

Para acessar o Dashboard de administração, fizemos o roteamento de 192.168.1.7:17070 para podermos acessar a dashboard pela rede do Insper. Assim, conseguimos o acesso pelo endereço `https://10.242.32.10:17070/` e pelo comando `juju show-controller --show-password` que nos deu a senha do usuário admin. 

#### 1. Qual o S.O. utilizado na máquina Juju? Quem o instalou?
O sistema operacional utilizado na máquina Juju é o Ubuntu, instalado pelo provedor de recursos da rede que é o MaaS.

#### 2. O programa juju client roda aonde? E o juju service? Como eles interagem entre si?
O juju client roda na máquina MaaS e o juju service roda na máquina juju. Os dois conseguem se comunicar via REST. Quando um o juju dá deploy em uma máquina, o service pede para a MaaS, seu provedor de recursos, por uma máquina.

#### 3. O que é LXC? e LXD?
LXC (Linux Conteiners) é uma biblioteca de baixo nível, que permite virtualizar o sistema operacional para rodar isoladamente múltiplos sistemas Linux (Conteiners) em uma máquina, usando um único Kernel Linux. Com isso, é possível ter diversas aplicações rodando simultaneamente, de forma isolada, controlando-as por meio de um Kernel só.

Já o LXD é construído em cima do LXC, com o intuito de fornecer uma melhor experiência ao usuário, criando e gerenciando conteiners de maneira mais fácil. Um dos features mais marcantes do LXD é que ele pode ser controlado por meio da internet de forma transparente, por meio de uma REST API.

## Deploying Wordpress com Load Balancing:

#### IP do wordpress via browser:
Pelo `juju status` temos que o IP da máquina HaProxy é http://192.168.1.11

#### 4. Explique o conceito por traz do HAProxy (Reverse Proxy). Vocês já fizeram algo parecido?
O HaProxy é responsável por fazer o redirecionamento de requisições do cliente para diferentes servidores da sua cloud. Fizemos algo parecido no roteiro 0 quando configuramos o load balancer, uma vez que esse usa o HaProxy para o redirecionamento.

#### 5. Na instalação, o Juju alocou automaticamente 4 máquinas físicas, duas para o Wordpress, uma para o Mysql e uma para o HAProxy. Considerando que é um Hardware próprio, ao contrário do modelo Public Cloud, isso é uma característica boa ou ruim?
Essa é uma característica ruim, uma vez que com uma private cloud você deveria ter customização máxima e inteiro controle sobre o seu pool de máquinas, ou seja, se você sabe que uma máquina é suficiente para rodar o Wordpress, você aloca só em uma ao invés de 2 como a juju faz.

#### 6. Crie um roteiro de implantação do Wordpress no seu hardware sem utilizar o Juju.
- Acessar via SSH uma máquina disponível
- Instalar via CLI o MySQL
- Acessar uma segunda máquina disponível via SSH
- Instalar via CLI o Apache Webserver
- Instalar via CLI o PHP e seus módulos
- Instalar via CLI o Wordpress
- Na página de admin do Wordpress linkar seu projeto ao banco de dados criado
- Permitir a conexão à máquina onde esta seu Wordpress via HTTP/HTTPS não somente via SSH

## Protótipo I
- Hugo Mendes: (repositório git)
- Martim José: https://github.com/martimfj/juju_charm_tutorial_vanilla

## Garbage Collector

## Questões Complementares
#### 1. Juju é uma aplicação distribuída? E o MaaS?
Juju é uma aplicação distribuída pois é apresentada para os usuários como um sistema único, mesmo que seja uma rede de computadores. A juju tem o seu juju client rodando na máquina do MaaS, enquanto o juju server roda em outra máquina. Isso é apresentado para o usuário como se fosse um sistema único e coerente.

O Maas está configurado em uma máquina só, não podendo ser identificado um sistema distribuído. Mas em grandes datacenters, o MaaS é distribuído em diversas máquinas, e sua interface com o usuário é única.

#### 2. Qual a diferença entre REST e RPC?
REST (Representational State Transfer) é uma arquitetura que define um jeito estruturado de representar os recursos ou coleções de recursos da solução. REST permite a relação entre cliente-servidor, onde os dados são disponibilizados em formatos como JSON e XML. Essa arquitetura modela as entidades como recursos e usa os verbos de HTTP para representar as transações dos recursos (GET para ler, POST para criar, PUT para mudar). Todos esses verbos são invocados na mesma URL e provocam diferentes funcionalidades.

Já o RPC (Remote Procedure Call) é um protocolo de comunicação entre processos, que permite um programa chamar um procedimento em outro computador ou rede, que é codificado como se fosse uma chamada de procedimento normal. Assim como o REST, esse protocolo é utilizado para implementação de soluções cliente-servidor. Em comparação com a REST, esse protocolo precisa de uma URL para cada requisição, porque não usa os verbos HTTP.

#### 3. O que é SOAP?
SOAP (Simple Object Access Protocol) é um protocolo para troca de informações estruturadas na implementação de serviços web em redes de computadores. Este protocolo usa XML como formato de mensagem e depende do protocolo da camada aplicação para fazer a negociação e transmissão de mensagens. O protocolo de aplicação mais utilizado é o RPC (Remote Procedure Call).

"O SOAP é protocolo baseado em XML consiste de três partes: um envelope, que define o que está na mensagem e como processá-la, um cabeçalho com conjunto de regras codificadas para expressar instâncias do tipos de dados definidos na aplicação, e um body com convenções para representar chamadas de procedimentos e respostas." -[Wikipedia](https://pt.wikipedia.org/wiki/SOAP)

## Concluindo
#### 1. O que é e o que faz um Deployment Orchestrator? Cite alguns exemplos.

É um sistema que gerencia o deploy de aplicações. Se encarrega de instalar as depêndecias, configurar os ambientes necessários, executar os passos de instalação na ordem correta, criar relações de dependência entre aplicações, fornecer informações sobre o status de cada deploy, comunicar-se com o gerenciador de bare
metal, entre outras coisas que facilitam a nossa vida. Exemplos de Deployment Orchestrators:
  - Juju
  - CloudAware
  - Kubernetes

#### 2. Como é o o processo de interação entre o MaaS e o Juju?
O MaaS é considerado um provedor de recursos, pois ele gerencia as máquinas da rede, que são os recursos. O juju manda uma requisição REST para o MaaS que cede uma máquina para o deploy da aplicação acontecer.

#### 3. Defina Aplicação Distribuída, Alta Disponibilidade e Load Balancing?
Uma aplicação distribuída é um conjunto de "blocos" independentes que trabalham em sinergia para fornecer ao usuário um sistema único e coerente. Por exemplo, quando o Facebook é acessado, há diversos servidores alocados pelo mundo que fornecem e recebem dados da aplicação. E tudo isso é feito de forma transparente para o usuário.

Alta disponibilidade é uma característica dada a sistemas resistentes a falhas de hardware e software, com o objetivo de manter seus serviços disponibilizados o máximo de tempo possível. Por exemplo a Amazon, eles tem uma disponibilidade de 99,99%.

O Load Balancing se utiliza do proxy reverso para a distribuição de carga e requisições no servidor. A idéia é que nenhuma máquina esteja sobrecarregada pois a demanda de recursos é distribuída dentre o pool de máquinas. Isso é um escalonamento horizontal.

#### Conclusão: O Juju utilizou o MaaS como provedor de recursos. O MaaS por sua vez forneceu o que havia disponível no rack. Você acha que seria necessária uma máquina de 32Gb para rodar um Apache Webserver ou um Load Balancer? Extrapole a resposta para um Datacenter real, onde as máquinas possuem configurações muito superiores. Como resolver esse problema?

Não é necessário ter uma máquina inteira para rodar um Apache Webserver ou um Load Balancer. Em um datacenter real, as máquinas são muito superiores do que a Intel NUC que temos no rack. Alocar uma máquina inteira para executar um serviço que não utiliza todos os recursos desta máquina é desperdício de dinheiro. Esse problema pode ser resolvido por de máquinas virtuais dentro de cada máquina física, assim compartilhando os recursos da máquina e fornecendo recursos on-demand. Isso pode ser feito por meio do openstack.