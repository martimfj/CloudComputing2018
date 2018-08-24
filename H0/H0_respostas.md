# Cap. 0 - Public Cloud Intro - 2 Aulas
### Hugo Mendes e Martim José
## Explorando o AWS:

#### 1. Descreva passo a passo o processo utilizado:
- Primeiramente acessamos o link disponibilizado na folha entregue pelo professor, link no qual fizemos o login no console da aws com informações que também constavam nessa folha.
- Acessamos então a pagina de IAM (Identity and Access Management) do console, clicando na aba Groups (menu lateral esquerdo) podemos criar nosso grupo.
- Dentro desse step temos que escolher um nome pro grupo (CloudComputing no nosso caso) e definir que tipos de permissão esse grupo pode ter (Administrator Access).
- Agora na aba de users, criamos um user para cada membro, um step bem simples, consistiu basicamente em dar nomes aos usuários, vincula-los à algum grupo, escolherá o tipo de permissão com o qual esses usuários acessarão o console (programmatic access ou Management Console Access) e salvar as keys geradas pelo console, baixamos um csv. No nosso caso criamos 4 usuários 2 para cada membro do grupo um do tipo programmatic e outro do tipo management.
- Na hora de acessar o console a primeira vez ele pedirá pra você mudar a senha que ele havia gerado automaticamente por uma de sua escolha.


#### 2. O que são Policies? Por que elas são importantes e devem ser bem definidas?
A policie da AWS é um objeto que quando atribuída à alguma entidade (como a Amazon chama) definem um conjunto de permissões que essa entidada tem. Existem 4 tipo de Policies da Amazon:

- Identity-based policies 
- Resource-based policies
- Organizations SCPs
- Access control lists (ACLs) 

Dentro de um projeto pode existir ações, arquivos, módulos ou informações privadas cuja só alguns membros do time podem ter acesso, a complicada e robusta estrutura de policies possibilita montar uma estrutura de gerenciamento de permissões bem completa, fornecendo a segurança de que só quem pode modificar um arquivo é quem realmente está o modificando ( ou lendo também). As policies apesar deserem divididas somente em 4 tipos se distrincham em uma variedade absurda de combinações de permissões, como são varios os serviços da AWS são varias as possibilidades de combinações de permissão para os usuários/grupos de um projeto. Deve atentar-se ao fato de que atribui-se à um usuário normalmente uma policie que representa um conjunto de permissões, é necessário ter em mente que dentro desse grupo de permissões pode ter algo que você não deseja, entretanto, a amazon estruturou esse conjunto de permissões em policies seguindo um conceito que considera ser o ideal, e é recomendável seguir essa estrutura.


## Primeira Instância

Endereço IP Público da maquina: 52.11.185.186


#### 1. Descreva passo a passo o processo utilizado.
Dentro do servico da EC2 clicamos em Launch Instance, em que primeiramente deve-se selecionar em qual máquina deseja rodar seu server, escolhemos a ubuntu 16 LTS, nos próximos steps foi selecionado o tipo (t2.micro), número de instancias (1) e o storage (8gb). Ignoramos a criação de tags e escolhemos a Rule padrão de um security group: type SSH procotol TCP. Review And Launch. Quando clicado em Launch você escolhe um nome pra sua .pem e a guarda em segurança.

#### 2. Dentro do contexto de Cloud Computing, defina os termos: instance, image, region, VPC, subnet, securitygroup.

- Instance: É um server virtual que roda numa cloud pública ou privada, por tratar-se de um software ele pode ser facilmente realocado em outras maquinas, tornando
    as instâncias dinâmicas.

- Image: Tomando como referência a AMI (Amazon Machine Image) entende-se que é um arquivo que mostra como serão configuradas a(s) instância(s) na cloud, no nosso caso EC2. Dentro desse arquivo contem informações como qual o sistema operacional, seu volume, permissões e aplicações que vão rodar.

- Region: "Cada região é uma área geográfica separada. Cada região contém vários locais isolados, conhecidos como zonas de disponibilidade.". As public ou privates clouds possuem data centers espalhadas em diferentes regiões por diversos motivos, proximidade de polos tecnológicos ( reduzindo distância aumenta-se performance) e precaução anti-catastrofe são dois exemplos.

- VPC: Que por extenso significa Virtual Private Cloud consiste em isolar uma rede virtual de forma lógica na cloud. É como se você tivesse seu próprio data center (uma private cloud) só que rodando em data centers de terceiros e usando toda sua infraestrutura escalável.

- Sub-rede: Um conjunto de IPs da sua VPC.

- Security-Group: Funcionam como um firewall virtual gerenciando o tráfego das instâncias que foram vinculadas à esse security-group. Para cada security-group você define regras que definem como o tráfego sera controlado. Essas regras podem ser adicionadas a qualquer momento, e depois de um tempo todas as instancias com aquele security group passara a ter  o trafego controlado segundo essas novas regras.

#### 3. O poder computacional das instâncias é medido em vCPU. O que é vCPU?
vCPU é só uma abreviação para CPUs virtuais, como vem dado para perceber muita coisa em cloud é virtual. A Amazon mede a capacidade computacional de uma CPU em ECUs ou EC2 units power o que equivaleria à um 2007 Intel Xeon ou AMD Opteron CPU operando de 1 GHz à 1.2 GHz, mas essa informação é meio antiga e essa medida pode ja ter    mudado, e varia muito de empresa para empresa. Cada VM na sua cloud possui uma vCPU ou mais, e o sistema operacional dessa VM encara essa cpu como se ela fosse fisica mesmo.

## Primeiro Deploy - Ghost Blog Platform

#### 1. Quantas instâncias foram criadas automaticamente? Criar instâncias automaticamente é um atributo positivo ou negativo?
Foram criadas 4 instâncias, sendo uma a inicial de controle da juju. Negativo, pela chance de criar mais instâncias do que se realmente precisa e você acabar pagando mais, uma vez que é cobrado pelo  uptime de cada instância.

#### 2. Quanto custou o protótipo? Assuma que usou uma hora de cada instância.
1 instancia t2.medium e 3 m3.medium em um hora custam convertendo para real (cotação atual do dolar de 3.7 reais) R$ 1.42598.

## Limpando a Bagunça

#### 1.Dada a quantidade de computadores apontada na questão anterior, descreva como você montaria um ambiente Ghost em um Datacenter próprio. Assuma que você ainda não possui nenhum hardware disponível, apenas um orçamento aprovado.
Comprariamos um hardware com as especifícações suficientes para o propósito da aplicação, e com ele criaríamos VMs como a amazon faz, destinando uma vm para o ghost, haproxy e sql. A questão é que devemos de alguma forma separar essas 3 partes do sistema, com a VM ganhamos com flexibilidade na atualização da capacidade computacional da máquina.

#### 2. Agora, somando o fato de que hardware deprecia com o tempo e possui um custo mensal de manutenção, compare em termos de custo uma Public Cloud e um Datacenter próprio.

Em termos de custos 3 máquinas compradas como a as disponibilizadas pela amazon saíriam cerca de 4500 mil reais, 90 reais de consumo de energia por mês, e estimando cerca de 500 reais de manutenção por ano (ja inclui as 3 maquinas) e 2880 reais com a banda.

Estima-se por ano um custo de 9 mil reais, com um data center próprio, enquanto com a amazon o pricing estimado foi de 8008 reais. Isso considerando que abstraímos muitos custos que um data center próprio tem, e usamos tudo da amazon.

## Escalabilidade

#### 1. O que faz o crontab?
O Crontab agenda tarefas para serem executadas a partir de eventos ou dias/datas determinadas pelo criador da tarefa.

## MONTANDO O AUTOSCALLING GROUP
Endereço: AutoScallingTeste-1320455852.us-east-1.elb.amazonaws.com

#### 1. O que é uma AMI?
AMI (Amazon Machine Image) é um "arquivo" base com as configurações de uma maquina (instancia), e o loadBalancer utiliza essas informações para fazer replicas dessa instância quando necessário. É o conceito de image já definido nas questões acima.

#### 2. O que faz o LoadBalancer? Explique o algoritmo utilizado
O loadbalancer divide o trafego da sua aplicação entre instâncias diferentes afim de não sobrecarregar nenhuma, caso essas instancias não sejam suficientes ele as réplica. O Load Balancer pode ser configurado para aceitar tráfego de entrada especificando por meio de um ou mais listeners. 

Em um Application Load Balancers, quando um nó do Load Balancer recebe uma solicitação, ele avalia as regras do listener em ordem de prioridade para determinar qual regra deve ser aplicada e em seguida seleciona um destino no grupo de destino para a ação da regra usando o algoritmo de roteamento. Todo roteamento é feito de forma independente para cada grupo de destino, mesmo que um destino seja comum entre grupos de destino.

## Fazendo uso da Escalabilidade Horizontal

#### 1. Enfim, como funciona o Autoscalling Group da AWS?
O auto scalling group monitora suas aplicações ajustando automaticamente a capacidade  de processamento/memoria de suas maquina, afim de manter uma performance estável, bem distrubuída e pelo menor custo possível. O auto scalling prevê o uso de cada recurso, baseado em inputs que o usuário fornece ou/e por inteligência da Amazon, que prevê a sua demanada em tempo real, permitindo que as alocações dos recursos da sua Cloud sejam otimizadas.

#### 2. Qual a diferença entre escalabilidade horizontal e escalabilidade vertical?
Escalabilidade vertical consiste em adicionar mais recursos a um sistema existente. Já a escalabilidade horizontal consiste em adicionar mais máquinas a um sistema. Por exemplo, quando um sistema mostra problemas de performance, pode-se adicionar mais CPU e memória ao sistema existente ou pode-se adicionar mais máquinas ao sistema, dividindo o processamento.

#### 3. Qualquer serviço pode fazer uso desse modelo?
Não, banco de dados é um exemplo em que escalabildidade horizontal é complicada. É dificil fazer a sincronização dos mesmos dados em diferentes máquinas, como garantir que um update em uma determinada maquina pode ser visto em real-time por alguem logado em outra maquina. Sincronização é o maior problema em escalabilidade horizontal, normalmente sistemas que possuem dados dependentes entre si vão enfrentar algum problema, mas não significa que não pode ser usado.

## Questões Complementares

#### 1. O que é um VPS? Qual a diferença entre uma instância e um VPS?
VPS (Virtual Private Server) é um servidor dedicado dividido em várias partes, cada uma atuando como um servidor individual. Ou seja, uma máquina rodando aplicações de vários clientes ao mesmo tempo. A grande diferença entre uma Instância (Cloud) e um VPS é onde e como a máquina opera. Em um Cloud Server os serviços dos clientes rodam em diversas máquinas físicas, que dividem recursos. Isso impacta no preço e na disponibilidade do serviço.

#### 2. Defina Platform as a Service (PaaS) e Software as a Service (SaaS).
"PaaS (Platform as a Service) consiste no serviço propriamente dito, de hospedagem e implementação de hardware e software, que é usado para prover aplicações (software como serviço) por meio da Internet." - Wikipedia

Platform as a Service é um meio termo entre o IaaS e o SaaS, ele permite que você gerencie sistemas que envolvem hardware através de uma interface (plataforma) que permite a configuração/costumização desse sistema.

SaaS (Software as a Service) são aplicações online que podem ser acessadas através da internet, o cliente apenas faz o uso desse software (produto) sem se preocupar com como foi feito, quais tecnologias foram usadas ou qual a infraestrutura por trás disso, ele simplesmente o uso como cliente final. O modelo de receita mais comum nesse tipo de serviço é por assinaturas mensais.

#### 3. O modelo LoadBalancer possui um custo fixo elevado. Como você justificaria o uso e a configuração de um LoadBalancer para uma empresa?
A palavra chave para essa questão é "Reliability". Você precisa passar confiança/segurança aos seus clientes de que os seus serviços estarão disponíveis não importando, o tráfego de pessoas que os usam, o objeto é um uptime de 100% ( mesmo sabendo que não é possível). Sistemas foras do ar geram prejuízos, e o LoadBalancer é uma ferramenta a mais que reduz as chances desse tipo de situações, justificando seu preço.
    
## Concluindo

#### 1. Defina Public Cloud. Cite a principal vantagem e desvantagem.
Public cloud é um ambiente completamente virtualizado, em que empresas fornecem recursos computacionais como serviço para qualquer organização/pessoa sendo normalmente no modelo pay-for-usage, ou seja, usou paga. Basicamente você terceiriza seu data center, ganhando até um certo ponto em custo, tempo e escalabilidade.

#### 2. Defina Infrastructure as a Service (Iaas).
O IaaS é um serviço responsável por fornecer toda infraestrutura  (servidores, rede, armazenamento e outros recursos de computação) para as seguintes camadas de serviço PaaS e SaaS. É prover toda a infraestrutura de hardware, incluindo sua manutenção e gerenciamento como parte do serviço. Através de IaaS obtem-se mais eficiência uma vez que os ambientes virtualizados "arrancam" o maximo de seus hardwares físicos.

#### 3. Defina Escalabilidade.
Escalabilidade é o poder de ter um sistema completamente responsivo à sua demanda de uso, a maleabilidade de poder disponibilizar mais recursos ou reduzi-los de forma rápida otimizando seus custos.

## Conclusão
#### Imagine como é o processo de alocação de uma instância. O que é realmente uma instância? Como você montaria um ambiente similar a AWS em um Datacenter próprio?
Uma instância é um server virtual rodando em alguma máquina virtual que por sua vez corresponde à um ambiente rodando em alguma máquina física. Caso fossemos montar um ambiente similar ao da amazon, comprariamos máquinas com poderes computacionais que fizessem sentido para o nosso serviço e estruturariamos essas máquinas físicas também em máquinas virtuais pois trata-las assim (do mesmo modo que a AWS) facilita aumentar ou diminuir suas configurações a partir da demanda de uso necessária.