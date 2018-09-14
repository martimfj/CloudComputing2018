# Cap. 2 - Deployment Orchestration - 3 Aulas
### Hugo Mendes e Martim José
## Instalando Juju:
Juju é uma ferramenta opensource de modelagem de aplicações, que permite implantar, configurar, dimensionar e operar infraestruturas em nuvem de maneira rápida e eficiente, seja em núvens públicas como privadas. É possível várias soluções por meio de simples comandos, como por exemplo instalar o wordpress (`juju deploy wordpress`).

Ao instalar o Dashboard de administração, nos deparamos com um erro na máquina, impossibilitando que a instalação fosse concluída. Para consertar isso, acessamos a máquina via ssh por meio do comando `juju ssh 0/lxd/0` e por meio do comando `sudo pip install six --ignore-installed` instalamos o pacote faltante sem verificar os outros necessários/pendentes.

Para acessar o Dashboard de administração, fizemos o roteamento de 192.168.1.7:17070 para podermos acessar a dashboard pela rede do Insper. Assim, conseguimos o acesso pelo endereço `https://10.242.32.10:17070/` e pelo comando `juju show-controller --show-password` que nos deu a senha do usuário admin. 

#### 1. Qual o S.O. utilizado na máquina Juju? Quem o instalou?
O sistema operacional utilizado na máquina Juju é o...

#### 2. O programa juju client roda aonde? E o juju service? Como eles interagem entre si?


#### 3. O que é LXC? e LXD?
LXC (Linux Conteiners) é um método de virtualizar o sistema operacional para rodar isoladamente múltiplos sistemas Linux (Conteiners) em uma máquina, usando um único Kernel Linux. Com isso, é possível ter diversas aplicações rodando simultaneamente, de forma isolada, controlando-as por meio de um Kernel só.

Já o LXD é... ? 

https://linuxcontainers.org/lxd/introduction/

## Deploying Wordpress com Load Balancing:

#### IP do wordpress via browser:

http://192.168.1.11  

que é o ip do hproxy.

#### 4. Explique o conceito por traz do HAProxy (Reverse Proxy). Vocês já fizeram algo parecido?

  O HaProxy é responsável por fazer o redirecionamento de requisições do cliente para diferentes servidores
  da sua cloud. Fizemos algo parecido no roteiro 0 quando configuramos o load balancer, uma vez que esse usa
  o HaProxy para o redirecionamento.


#### 5. Na instalação, o Juju alocou automaticamente 4 máquinas físicas, duas para o Wordpress, uma para o Mysql e uma para o HAProxy. Considerando que é um Hardware próprio, ao contrário do modelo Public Cloud, isso é uma característica boa ou ruim?

Ruim, uma vez que com uma private cloud você deveria ter customização máxima e inteiro controle sobre o seu
pool de maquinas, ou seja, se você sabe que o wordpress será o suficiente somente em uma máquina, você o deploya só em uma ao invés de 2 como a juju faria.

#### 6. Crie um roteiro de implantação do Wordpress no seu hardware sem utilizar o Juju.
- Acessar via SSH uma maquina disponivel
- Installar via CLI o mysql
- Acessar uma segunda maquina disponível via SSH
- Instalar via CLI o Apache Webserver
- Instalar via CLI o Php e seus modulo
- Instalar via CLI o wordpress
- Na pagina de admin do wordpress linkar seu projeto ao banco de dados criado
- Permitir a conexão à maquina onde esta seu wordpress via HTTP/HTTPS não somente via SSH

## Protótipo I
- Hugo Mendes: (repositório git)
- Martim José: (repositório git)

## Garbage Collector

## Questões Complementares
#### 1. Juju é uma aplicação distribuída? E o MaaS?

#### 2. Qual a diferença entre REST e RPC?

#### 3. O que é SOAP?

## Concluindo
#### 1. O que é e o que faz um Deployment Orchestrator? Cite alguns exemplos.

#### 2. Como é o o processo de interação entre o MaaS e o Juju?

#### 3. Defina Aplicação Distribuída, Alta Disponibilidade e Load Balancing?

#### Conclusão: O Juju utilizou o MaaS como provedor de recursos. O MaaS por sua vez forneceu o que havia disponível no rack. Você acha que seria necessária uma máquina de 32Gb para rodar um Apache Webserver ou um Load Balancer? Extrapole a resposta para um Datacenter real, onde as máquinas possuem configurações muito superiores. Como resolver esse problema?