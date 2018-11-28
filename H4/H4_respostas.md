# Cap. 4 - Cloud Outtro - 2 Aulas
### Hugo Mendes e Martim José

## Questões finais
#### 1. Qual o conceito por trás de Edge Computing? (Obs: Não é a rede de celular 2G)
A ideia do Edge Computing é diminuir a latência da rede. Isso pode ser alcançado descentralizando a cloud e utilizando nós distribuídos que estejam mais próximos dos dispositivos/aplicações que estão utilizando dados da nuvem. Estes nós mais próximos podem ser cloudlets ou outros dispositivos que ja tenham a informação, evitando que outro dispositivo tenha que fazer o caminho completo até um datacenter sendo que ja tem essa informação ali próximo.

#### 2. Você é o CTO (Chief Technology Officer) de uma grande empresa com sede em várias capitais no Brasil e precisa implantar um sistema crítico, de baixo custo e com dados sigilosos para a área operacional. Você escolheria Public Cloud ou Private Cloud?
A escolha ideal seria uma cloud híbrida, dado que o sistema é crítico, a alta disponibilidade é indispensável, baixo custo é desejável e armazena dados sigilosos.
Para isso, a Cloud Pública assumiria as questões de alta disponilididade e escalabilidade visto que há diversos datacenters expalhados pelo Brasil, com serviços de manutenção, loadbalancing, etc. A Cloud Privada seria responsável para armazenar os dados sigilosos da empresa seguindo as regras de compliance da empresa e faria a comunicação criptografada via API com os servidores da Cloud Pública.

#### 3. Agora explique para ao RH por que você precisa de um time de DevOps.
Prezado responsável pelo RH, 

Para implementar a infraestrutura necessária para hostear os serviços da empresa, é preciso de um time multi disciplinar (software, infraestrutura e QoS) altamente integrada que consiga desenvolver e manter as aplicações e serviços. Entre tarefas mais importantes estão:
* Balanceamento de Carga 
* Planejamento de infraestrutura da rede da empresa
* Implementação e manutenção de servidores privados
* Gerenciamento de bare metal
* Orquestramento de deploy
* Orquestramento de container
* Virtualização de máquinas
* Health checks

Entre as habilidades estão:
* Conhecimento de ferramental da cloud pública e privada
* Integração contínua
* Implantação contínua
* Base sólida em aplicações distribuídas

#### 4. Junte o seu time e monte um plano para DR e HA da empresa. Explique o que é SLA e onde se encaixa nesse contexto.

**Plano de DR:**
Teríamos um datacenter (cloud privada) principal rodando a aplicação em produção, e teríamos um data center de outra localização e provedora com dados replicados.     Para os que estavam guardados na cloud privada, teriamos dois centros de infraestrutura em diferentes estados, separados por pelos menos 300km de distância, sendo que um deles também funcionaria como uma réplica sincronizada.

**Plano de HA:**
A ideía de usar servidores da cloud publica para dados não sensíveis é se utilizar dos recursos que ja nos disponibizam de modo fácil, terceirizar o máximo possível a dor de cabeça com HA dos servidores, deixando que o service contratado se encarregue disso. Para os servidores privados a idéia seria redirecionar o tráfego para o servidor replica enquanto o principal fica em manutenção. Dependendo do orçamento poderia haver mais de uma réplica.

O SLA ou Acordo de nível de serviço é um contrato assinado entre o prestador do serviço e o contrante, em que se estipula níveis mínimos de de qualidade
para o serviço que foi contratado, estando mais relacionado a Requisitos Não Funcionais do sistema. Caso o mesmo não seja entregue pela empresa o cliente
pode cobrar uma indenização.

## Projeto Final
#### 1. Dos requisitos de projeto acima, quais são funcionais e quais são não funcionais?
**Requesitos funcionais:**
* API REST
* Aplicação que consome o serviço

**Requesitos não funcionais:**
* Seja distribuído
* Seja elástico
* Script de implementação
* Migrável para outra nuvem

#### Repositório dos projetos:
* Hugo Mendes:
* Martim José: https://github.com/martimfj/R2Image