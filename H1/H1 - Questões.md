
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

#### 2. Qual a diferença entre um IP público e um IP privado?

#### 3. Qual a classe utilizada na rede interna do Insper? E na sua rede? Quantas classes existem?

## Instalando o MaaS
#### 1. Descreva como foram evitados ou resolvidos os problemas de roteamento e resolução de nomes.
Para resolver os problemas de roteamento, foi preciso configurar um IP fixo para máquina, os DNS e o Gateway a serem utilizados. Para isto, foi preciso editar o arquivo que determina estas configurações:
```
cd ~./ect/netplan
sudo nano 50-cloud-init.yaml
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

## Chaveando o DHCP
#### 1. Por que Desabilitar o do roteador?

#### 2. Como funciona o ataque DHCP rogue? Como evitar?



## Comissioning nodes
#### 1. Descreva o processo PXE Boot? Qual a sua grande vantagem em um datacenter real?

#### 2. Analisando em um aspecto mais amplo, quais outras funcionalidades do MaaS pode ser útil no gerenciamento
de bare metal?

## Finalizando a rede para acesso “externo”.
#### 1. Qual o nome e como funciona a ferramenta utilizada?

#### 2. O que deveria ser feito para você conseguir acessar o Maas da sua casa?


## Questões Complementares
#### 1. O que significa LTS? Por que isso importa para uma empresa?
LTS significa Long Term Support é um tipo de contrato que a empresa mantenedora do software tem com seus usuários, que garante a manutenção e atualização do software por um perído de tempo maior, mantendo a integridade do mesmo, sem implementar mudanças bruscas como features novas.

#### 2. O que é IPv6? Qual a importância da migração?
O padrão atual do IP (Internet Protocol) é a versão 4 (IPv4) que tem um tamanho de 32 bits, permitindo que 2^32 dispositivos sejam mapeados por meio de IP Addresses. Mas a partir da década de 90 em que ja previam que com a ascenção da Internet, uma hora os quase 4 bilhões de possíveis hosts iriam se esgotar. Portanto, recorreu-se a uma nova versão do protocolo, chamada IPv6, onde o endereço passa ter tamanho 128 bits, permitindo muitos novos endereços. Ele também reduzuz o tamanho das tabelas de roteamento, aumenta a velocidade de processamento de pacotes pelos roteadores, é mais seguro, é mais fácil de ser evoluído e permite a coexistência de diferentes protocolos. A migração é de extrema importância para a internet não estagnar em número de usuários. 

#### 3. A literatura preconiza que o Modelo de Rede Internet possui 5 camadas, quais são elas e quais camadas foram envolvidas nesse capítulo?

#### 4. A literatura mais antiga discorre sobre o Modelo de Rede OSI de 7 camadas. Explique a diferença entre os dois modelos.


## Concluindo
#### 1. O que é e para que serve um gerenciador de Bare Metal?

#### 2. O que é um MAC address?
É um número de identificação único designado ao NIC (Network Interface Controller) de um dispositivo, sendo usado como um endereço de rede, para controle de acesso em redes de computadores. 

#### 3. O que é um IP address? Como ele difere do MAC address?
	IP -> Pg. 277
  
O IP (Internet Protocol) Address é um endereço de protocolo da internet que identifica cada host conectado à rede, permitindo que elas se comuniquem e troquem informações. O IP é um identificador virtual que permite a comunicação e localização das partes. Já o MAC Address é um identificador físico, "imutável" que identifica placa de rede das máquinas ligadas a internet.

#### 4. O que é CIDR? Qual o papel da subrede?
	CIDR -> Pg. 279

#### 5. O que é são DHCP, DNS e gateway?
	DHCP -> Pg. 294