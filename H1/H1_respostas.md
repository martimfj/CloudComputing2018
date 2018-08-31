
# Cap. 1 - Bare Metal - 5 Aulas
### Hugo Mendes e Martim José
## Material:
#### 1. Como foi feito para identificar as NUCs corretamente sem um sistema operacional?
Sem o sistema operacional, podemos obter informações sobre o sistema por meio de BIOS (Basic Input/Output System).


## Rede:
#### Fazer um desenho prévio de um diagrama que representa a montagem física dos equipamentos.

#### 1. Quais IPs são fixos e quais são flutuantes? Qual a subrede?
O IP fixo é o da Rede ao qual nosso roteador está ligado, que nesse caso é a rede do Insper, todos os outros ips abaixo do rote

#### 2. Existe um DHCP server na sua rede? Aonde?
Existem o  DHCP server da nossa rede está dentro do Roteador Asus que dinamicamente aloca diferentes hosts para as maquinas conectadas à  ele.

#### 3. Existe um DNS server na sua rede? Aonde?
Não, Domain Name Registration é um banco de dados que traduz um IP em um nome mais humano, nossa subrede cuida simplesmente da parte de Rede do modelo OSI, não precisamos tratar isso.

#### 4. Existe um gateway? Aonde?
Existe, no Roteador. O Roteador é o responsável por gerenciar diferentes pedidos (request, trocas de pacote) da sua sub-rede, no final das contas ele centraliza tudo nele, e por informações que cada pacote tem (o que inclui de qual host esse pacote  vêm, e para qual ele vai) o roteador faz intermediação.

#### 5. Qual a topologia da sua rede?
Topologia estrela, as NUCS estão todas diretamente ligadas em um computador central (Roteador ASUS) com a intermediação de um switch por falta de entrada de cabos ethernet.

## Lapidando o projeto
#### 1. Quantos IPs utilizáveis estão disponíveis na subrede 192.168.0.0/20? Todos os IP são utilizáveis?
4094 ips. 2 ips são reservados e não utilizaveis, o primeiro e o ultimo, sendo o primeiro usado para identificação e o ultimo para broadcast.

#### 2. Qual a diferença entre um IP público e um IP privado?

Um ip publico são aqueles externos a sua subrede, de livre conhecimento, normalmente estão atrelados aos gateways. Ja os ips privados são internos à sua rede. A tradução
entre esses dois tipos normalmente acontece no gateway através do NAT ( network address translation ).

#### 3. Qual a classe utilizada na rede interna do Insper? E na sua rede? Quantas classes existem?

O Insper utiliza uma rede classe B, a nossa é uma CIDR, uma vez que utiliza uma mascara de rede customizada. Ao todo são 4 tipos de classe sem contar com a CIDR.

## Instalando o MaaS
#### 1. Descreva como foram evitados ou resolvidos os problemas de roteamento e resolução de nomes.
Para resolver os problemas de roteamento, foi preciso configurar um IP fixo para máquina, os DNS e o Gateway a serem utilizados. Para isto, foi preciso editar o arquivo que determina estas configurações:
```
> cd /ect/netplan
> sudo nano 50-cloud-init.yaml
```
O arquivo de configuração foi editado, desativando o DHCP, setando o IP da máquina para `192.168.0.3/20`, o Gateway para `192.168.0.1` (IP do Roteador) e os DNS para `8.8.8.8` e `8.8.4.4`.

Sendo assim, o arquivo ficou da seguinte maneira:
```
network:
  version: 2
  ethernets:
    enp0s3:
     dhcp4: no
     addresses: [192.168.0.3/20]
     gateway4: 192.168.0.1
     nameservers:
       addresses: [8.8.8.8,8.8.4.4]
```

Para aplicar as configurações, bastou rodar o comando `sudo netplan apply`

## Configurando o MaaS
#### Verifique se o SSH está funcionando. Aonde você deve conectar o seu notebook?
Podemos conectar o computador ao MaaS por meio do SSH, fazendo o `ssh cloud@192.168.0.3
`. 

#### Chave pública gerada:
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCq20Zts/xPSZOzjtvQl5Lh2XcEzIxvM/WOlMssg53V5PgbXIPXWjYNpeMsEB5gm6EdmD+GXrOqMolSbQUVQVT7LcDlnDetRE6w9C58D6ZhunuRGHKSCI8HlS4wIXZHBbj3Vor8xxFKMrgsQSt1BUKV7U7cfKZgvNof+rHkQf77Wsm13xX4qdhxY1+CBW/7/c5LSPqjZVqmJfq7R8/v30D7Ccnhn5Oqgnm609/rGSVUklw4/qW+uQHfasRcHo76RDP0UDgpvBQUNL7Rxnqk5Z/trX1mrDGsJqOJiMDSNDvJlV648w6hVEHzK66gW6SnExY6HZxRA8/5K5b8urnXkjTj cloud@cloud

## Chaveando o DHCP
#### 1. Por que Desabilitar o DHCP do roteador?
Caso exista mais de um DHCP Server na mesma subrede, há grandes possibilidades de conflitos de IP. Pois diferentes DHCP Servers podem atribuir um mesmo IP para máquinas diferentes e IPs diferentes para uma mesma máquina. Para resolver isso, podemos distribuir diferentes faixas de IPs para cada DHCP, mas no nosso caso, queremos que a maas controle as máquinas.

#### 2. Como funciona o ataque DHCP rogue? Como evitar?
Quando usuários se conectam à rede, o falso servidor DHCP e o verdadeiro irão oferecer endereços IP. Quando a resposta do falso servidor DHCP chega primeiro no usuário ele irá ignorar a resposta do DHCP verdadeiro da rede e estará na rede do atacante. Dessa maneira o atacante passa ser o gateway padrão da vítima, podendo implementar um ataque do tipo Man-in-the-Middle. O atacante também pode atuar como um servidor DNS, podendo encaminhar a vítima para endereços maliciosos. Existe uma função hoje em dia chamada DHCP Snooping que rastreia endereços MAC, aribuições de endereços IP e portas correspondentes, garantindo que apenas combinações legítimas possam se comunicar.

## Comissioning nodes
#### 1. Descreva o processo PXE Boot? Qual a sua grande vantagem em um datacenter real?
PXE Boot é um método de boot remoto desenvolvido pela Intel, que permite que o PC boot através da rede, carregando todo o software necessário a partir de um servidor previamente configurado, que neste caso foi o Maas. Com o Maas ligado e com as outras máquinas configuradas para bootar por meio da rede, ligou-se cada uma delas que começaram a enviar pacotes de broadcast pela rede, informando que estavam "aptas" a serem controladas. Ao identificar as máquinas da rede que podem ser controladas, o Maas as identificou por meio do MAC Address. Assim, pudemos nomear as máquinas a partir disso, atribuir um IP stático para cada uma delas e habilitar o Intel AMT que permite o Maas ligar e desligar cada uma delas.

Em um datacenter real, o PXE é extremamente útil, pois como ele permite que todas as máquinas possam ser bootadas remotamente, sem necessidade de instalar o sistema operacional por meio de um pendrive.

#### 2. Analisando em um aspecto mais amplo, quais outras funcionalidades do MaaS pode ser útil no gerenciamento de bare metal?
Por meio do Intel AMT, o Maas consegue gerenciar a energia das máquinas, podendo liga e desligar cada uma remotamente. Além disso, é possível automatizar a descoberta e registro de cada dispositivo na rede, fazer o deply de uma máquina remotamente, configurar interface de rede das máquinas, gerenciar a rede, fazer integrações com outras ferramentas de automação (clojure, juju...) e monitora os serviços críticos da rede de computadores.

## Finalizando a rede para acesso “externo”.
#### 1. Qual o nome e como funciona a ferramenta utilizada?
Para acessar o Maas remotamente, por meio de outra rede, foi preciso fazer o roteamento de portas no roteador. Redirecionando todo acesso da porta 22 feito no IP do roteador (10.242.32.3) para o IP do Maas (192.168.0.3), também na porta 22.

#### 2. O que deveria ser feito para você conseguir acessar o Maas da sua casa?
Para conseguir acessar o Maas da nossa casa, o administrador de redes do Insper, deveria abrir a porta 22 da rede Insper e redirecionar para a o Maas, que está em uma das várias subredes que existem no complexo. Mas como isso seria uma grande vulnerabilidade para a Instituição, não é viável concretizar tal coisa.

## Questões Complementares
#### 1. O que significa LTS? Por que isso importa para uma empresa?
LTS significa Long Term Support é um tipo de contrato que a empresa mantenedora do software tem com seus usuários, que garante a manutenção e atualização do software por um perído de tempo maior, mantendo a integridade do mesmo, sem implementar mudanças bruscas como features novas.

#### 2. O que é IPv6? Qual a importância da migração?
O padrão atual do IP (Internet Protocol) é a versão 4 (IPv4) que tem um tamanho de 32 bits, permitindo que 2^32 dispositivos sejam mapeados por meio de IP Addresses. Mas a partir da década de 90 em que ja previam que com a ascenção da Internet, uma hora os quase 4 bilhões de possíveis hosts iriam se esgotar. Portanto, recorreu-se a uma nova versão do protocolo, chamada IPv6, onde o endereço passa ter tamanho 128 bits, permitindo muitos novos endereços. Ele também reduzuz o tamanho das tabelas de roteamento, aumenta a velocidade de processamento de pacotes pelos roteadores, é mais seguro, é mais fácil de ser evoluído e permite a coexistência de diferentes protocolos. A migração é de extrema importância para a internet não estagnar em número de usuários. 

#### 3. A literatura preconiza que o Modelo de Rede Internet possui 5 camadas, quais são elas e quais camadas foram envolvidas nesse capítulo?

As camadas preconizadas são as seguintes:

Física -> Enlace -> Rede -> Transporte -> Aplicação.

Nesse roteiro foram envolvidas basicamente todas as camadas, desde à fisica à aplicação. Entretanto o foco foi da física à rede.

Física -> hardwares físicos, ligamento de cabos etc.
Enlace -> Switch
Rede -> Roteador.
Transporte ->  protocolos de comunicação
Aplicação -> dashboards sendo usadas para o management das outras camadas.

#### 4. A literatura mais antiga discorre sobre o Modelo de Rede OSI de 7 camadas. Explique a diferença entre os dois modelos.

A diferença é que no modelo osi, a camada mais superior do modelo híbrido ("Aplicação"), é dividida em 3 camadas: Sessão, Apresentação e Aplicação. Essas camadas Sessão e
Apresentação foram basicamente ignoradas.


## Concluindo
#### 1. O que é e para que serve um gerenciador de Bare Metal?
    O próprio Maas (metal as a service) é um gerenciador de bare metal. Serve para a automação de servidores físicos, aumenta sua eficiência operacional. Ele te permite
    ter a flexbilidade da cloud (claro que não toda) com a eficiência dos servidores físicos.

#### 2. O que é um MAC address?
É um número de identificação único designado ao NIC (Network Interface Controller) de um dispositivo, sendo usado como um endereço de rede, para controle de acesso em redes de computadores. 

#### 3. O que é um IP address? Como ele difere do MAC address?
dx
O IP (Internet Protocol) Address é um endereço de protocolo da internet que identifica cada host conectado à rede, permitindo que elas se comuniquem e troquem informações. O IP é um identificador virtual que permite a comunicação e localização das partes. Já o MAC Address é um identificador físico, "imutável" que identifica placa de rede das máquinas ligadas a internet.

#### 4. O que é CIDR? Qual o papel da subrede?
	É uma classificação dada para redes que não são do tipo A, B ou C, ou seja,usam uma mascara de rede customizada. A tradução do seu acrônimo é: 'Classless Inter-Domain
    Routing'.
    Quando uma rede se torna muito grande e o desempenho começa a cair como consequência de muito tráfego, pode-se resolver o problema dividindo a rede em partes menores.
    Existem várias técnicas para dividir uma rede, e a subrede é uma delas. A sub-rede é basicamente apenas uma maneira de dividir uma rede TCP / IP em partes menores e mais gerenciáveis. 
    A ideia básica é que, se você tiver uma quantidade excessiva de tráfego fluindo pela rede, esse tráfego poderá fazer com que a sua rede fique lenta.
     Quando divide sua rede em subrede, você está dividindo a rede em uma rede separada, mas interconectada.
    

#### 5. O que são DHCP, DNS e gateway?
	O DCHP (dynamic host configuration protocol) server é o responśavel por fornecer os ips das maquinas conectadas à sub-rede à qual pertence.
    DNS (domain name system) é um sistema que traduz ips em nomes 'legíveis',  dhcps servers podem acompanhar um DNS server também.
    Um gateway é um dispostivo encarregado de estabelecer a comunicação entre duas redes, respeitando protocolos específicos e tomando decisões para que
    as duas pontas funcionem. Resumindo,ele faz o papel de ponte.

