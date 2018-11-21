# Cap. 3 - Private Cloud Stack - 4 Aulas
### Hugo Mendes e Martim José
## Instalando Canonical Distro:
Para baixar o charm do Openstack:
* `wget https://api.jujucharms.com/charmstore/v5/openstack-base/archive`
* `unzip archive`

Escolhemos começar um controller novo:
* `juju kill-controller`
* `juju bootstrap maas main --to juju`

Instalando o bundle customizado:
* `juju deploy bundle.yaml`

Instalando o client do Openstack:
* `sudo apt  install python3-openstackclient`
* `sudo apt install python-novaclient python-keystoneclient python-glanceclient python-neutronclient python-openstackclient -y` (instala as ferramentas necessárias para o client)

Para criar as variáveis do ambiente:
* `cd ~openstack` (diretório onde o charm foi extraído)
* `source openrc`
* `env` (imprime as variáveis de ambiente)
* `openstack catalog list` (lista os serviços)

#### 1. Faça um desenho de como é a sua arquitetura de solução, destacando o hardware, sistema operacional/container e respectivas alocações dos serviços.
![Diagrama de Arquitetura de Solução](diagrama_solucao.png)

## Configurando o Openstack (https://jujucharms.com/openstack-base/)
Para importar a imagem do Ubuntu 16:
```
curl http://cloud-images.ubuntu.com/xenial/current/xenial-server-cloudimg-amd64-disk1.img | \
    openstack image create --public --container-format=bare --disk-format=qcow2 xenial
```

Para importar a imagem do Ubuntu 18:
```
curl http://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img  | \
    openstack image create --public --container-format=bare --disk-format=qcow2 bionic
```

Para configurar uma rede "externa" e roteador compartilhado (na pasta openstack):
```
./neutron-ext-net-ksv3 --network-type flat -g 192.168.0.1 -c 192.168.0.0/20 -f 192.168.8.1:192.168.9.254 ext_net
```

Para configurar a rede interna (na pasta openstack):
```
./neutron-tenant-net-ksv3 -p admin -r provider-router -N 1.1.1.1 internal 192.169.1.0/24
```

Para configurar os flavors (instance type):
* openstack flavor create --ram <size-mb> --disk <size-gb> --ephemeral-disk <size-gb> --vcpus <num-cpu> <flavor-name>

* `openstack flavor create --ram 1024 --disk 20 --ephemeral 0 --vcpus 1 m1.tiny`
* `openstack flavor create --ram 2048 --disk 20 --ephemeral 0 --vcpus 1 m1.small`
* `openstack flavor create --ram 4096 --disk 40 --ephemeral 0 --vcpus 2 m1.medium`
* `openstack flavor create --ram 8192 --disk 40 --ephemeral 0 --vcpus 4 m1.large`

Para criar um Key-pair usando a *public key* do próprio MaaS:
* `openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey`

(O ideal era ter uma keypair para cada instância, mas como estamos em um ambiente controlado, podemos fazer assim. De modo que o MaaS consiga acessar todas as instâncias com uma chave só.)

Via o comando `juju status` é possível ver que a Dashboard é acessível pelo ip *192.168.1.30*. Para acessar remotamente, foi feito um port foward no roteador:
* 10.242.32.10:80 => 192.168.1.30:80
* 10.242.32.10/horizon

Os dados de login da dashboard estão nas variáveis `env`:
* domain: admin_domain -> [OS_USER_DOMAIN_NAME]
* username: admin -> [OS_USERNAME] 
* senha: [OS_PASSWORD]

Para liberar o acesso SSH, ICMP e DNS:
* Project > Network > Security Groups > Default - Manage Rules
* Para cada nova regra: Add Rule > Rule [SSH, ICMP, DNS] > Add (0.0.0.0/0 para todos os IPs)
  
Para disparar uma instância tiny sem volume adicional:
* http://10.242.32.10/horizon/project/instances/
* Source > Delete Volume on Instance delete: `No`
* Source > Allocated: `Bionic`
* Network > Alocated: in_net
* Flavor: m1.tiny
* Key Pair > Create Key Pair: MyKey
  
Para alocar um IP flutuante para instância:
* Instance (em questão) > Associate Floating IP
* Crie o IP (+) e associe à instância
* Floating IP associado: `192.168.8.1`
  
Assim, o IP interno da instância na rede openstack (*192.169.1.0/24*) é *192.169.1.19*. Estando na rede externa (*192.168.0.0/20*), é possível conectar na rede interna por meio de um roteador e acessar a instância criada. O esquema pode ser visto abaixo:
![Network topology](network_topology.jpeg)

Para testar a conexão SSH, podemos conectar na rede interna *192.168.0.0/20* e depois na instância criada (que está na rede interna do Openstack), por meio de:
* `ssh cloud@10.242.32.10`
* `ssh ubuntu@192.168.8.1` (floating IP)

#### 2. Faça um desenho de como é a sua arquitetura de rede, desde a conexão com o Insper até a instância alocada.
![Diagrama de Rede](diagrama_network.png)

## Criando usuários

A interface para criar um usuário e um projeto:
* Identity > Users > Create User

Para testar a conexão SSH com a instância, foi preciso transferir a private key pair para a máquina cloud:
* `scp Downloads/keypairmartim.pem scp://cloud@10.242.32.10//home/cloud/martimfjkeypair.pem`

Para conectar na instância pela máquina Cloud:
* chmod 400 martimfjfjkeypair.pem
* ssh ubuntu@192.168.8.12 -i martimfjkeypair.pem

#### 3. Monte um passo a passo de configuração de rede via Horizon.
Network > Network Topology > Create Network
* Network Name: `intnet_martim`
* Subnet Name: `subnet_martim`
* Network Address: `192.171.0.0/24`
* Allocation Pools: `192.171.0.3, 192.171.0.254`
* DNS Name Servers: 
``` 
192.171.0.2
192.168.0.3
8.8.8.8
```

Network > Network Topology > Create Router
* Router Name: `router_martim`
* External Network: `ext_net`

Clique no roteador e adicione uma interface:
* Subnet: `intnet_martim: 192.171.0.0/24`
* IP Address: `192.171.0.1`

## Protótipo II
* Hugo Mendes: (repositório git)
* Martim José: https://github.com/martimfj/openstack_shadeapp

## Deja-vu (Juju Reborn)
#### 4. Escreva as configurações utilizadas para incluir o Openstack como Cloud Provider no Juju.

Para acessar a instância *test* criada no início do roteiro:
* `ssh ubuntu@192.168.8.1`

Instalando o juju e o juju-gui:
* `apt install python-software-properties`
* `sudo add-apt-repository ppa:juju/stable`
* `sudo apt-get update && sudo apt-get install`
* `juju-core`
* `juju generate-config`

Adicionando o Openstack como *Cloud Provider* no Juju:
* `juju add-cloud maas ~/.local/share/juju/clouds.yaml`

Para criar um serviço Simplestreams:
* ``

#### 5. Escreva o comando de bootstrap.
Para instalar o Kubernetes core, acesse a máquina criada via SSH:
* `juju deploy kubernetes-core`

Para testar o acesso do Kubernetes:
* `mkdir -p ~/.kube`
* `juju scp kubernetes-master/1:config ~/.kube/config`
* `snap install kubectl --classic`
* `kubectl cluster-info`

## Escalando o Kubernetes
Entrando no Overview da dashboard do Openstack, é possível ver a quantidade de recursos sendo utilizadas (CPUS: 8/20, RAM: 17/50)

Na aba de instâncias do dashboard, é possível ver que o kubernetes worker utiliza uma máquina m1.large (4 CPUS, 8 RAM, 40 Disk). Portanto, podemos subir mais 3 instâncias m1.large, ficando com (CPUS: 20/20, RAM: 41/50)

Para escalar horizontalmente o Kubernetes:
* `juju add-unit kubernetes-worker`

#### 6. O que é um Hypervisor? Qual o hypervisor do Openstack, da AWS e da Azure?
Hypervisior é uma camada de software entre o hardware e o sistema operacional. É responsável por fornecer ao sistema operacional a abstração da máquina virtual, ou seja, ele controla os recursos de hardware que os SO podem acessar. Existem vários Hypervisiors no mercado, tanto open source quanto pago.

O Openstack permite a utilização de diversos tipos de Hypervisiors, mas o mais utilizado é o KVM. A AWS, criou um dedicado para solução deles baseando-se no KVM. E a Azuere, criou um similar ao Microsoft Hyper V, chamado Azure Hypervisior customizado para a plataforma deles.

## Habilitando o LoadBalancer
#### 7. Escreva o seu roteiro detalhado de instalação e testes


## Questões Complementares
#### 1. Assistir o vídeo: https://youtu.be/ZlCoIIgLzYQ

#### 2. Dado que vocês trabalharam com Nuvem Pública e com Nuvem Privada, descreva com detalhes como você montaria uma Nuvem Híbrida. Como seria a troca de dados?

#### 3. É possível somar todo o hardware disponível e disparar uma instância gigante (ex: mais memória do que disponível na melhor máquina)? Discorra sobre as possibilidades.

#### 4. Como visto é possível rodar o Juju sobre o Openstack e o Openstack sobre o Juju. Quais os empecilhos de ter um Openstack rodando sobre outro Openstack?


## Concluindo
#### 1. Cite e explique pelo menos 2 circunstâncias em que a Private Cloud é mais vantajosa que a Public Cloud.

#### 2. Openstack é um Sistema Operacional? Descreva seu propósito e cite as principais distribuições?
O Openstack não é um sistema operacional de um computador, mas ele pode ser considerado um sistema operacional de uma nuvem, permitindo que os usuários deem deploy em máquinas virtuais e outras instâncias que lidam com diversas tarefas no gerenciamento do ambiente cloud. A plataforma facilita a escalabilidade horizontal, o que significa que as tarefas que se beneficiam da execução simultânea podem facilmente servir mais ou menos usuários rapidamente simplesmente acionando mais instâncias.

O Openstack tem diversas distribuições desenvolvidas por grandes empresas como Canonical, IBM, Oracle, Red Hat, VMware e outras.

#### 3. Quais são os principais componentes dentro do Openstack? Descreva brevemente suas funcionalidades.
Nova is the primary computing engine behind OpenStack. It is used for deploying and managing large numbers of virtual machines and other instances to handle computing tasks.

Swift is a storage system for objects and files. Rather than the traditional idea of a referring to files by their location on a disk drive, developers can instead refer to a unique identifier referring to the file or piece of information and let OpenStack decide where to store this information. This makes scaling easy, as developers don’t have the worry about the capacity on a single system behind the software. It also allows the system, rather than the developer, to worry about how best to make sure that data is backed up in case of the failure of a machine or network connection.

Cinder is a block storage component, which is more analogous to the traditional notion of a computer being able to access specific locations on a disk drive. This more traditional way of accessing files might be important in scenarios in which data access speed is the most important consideration.

Neutron provides the networking capability for OpenStack. It helps to ensure that each of the components of an OpenStack deployment can communicate with one another quickly and efficiently.

Horizon is the dashboard behind OpenStack. It is the only graphical interface to OpenStack, so for users wanting to give OpenStack a try, this may be the first component they actually “see.” Developers can access all of the components of OpenStack individually through an application programming interface (API), but the dashboard provides system administrators a look at what is going on in the cloud, and to manage it as needed.

Keystone provides identity services for OpenStack. It is essentially a central list of all of the users of the OpenStack cloud, mapped against all of the services provided by the cloud, which they have permission to use. It provides multiple means of access, meaning developers can easily map their existing user access methods against Keystone.

Glance provides image services to OpenStack. In this case, "images" refers to images (or virtual copies) of hard disks. Glance allows these images to be used as templates when deploying new virtual machine instances.

Ceilometer provides telemetry services, which allow the cloud to provide billing services to individual users of the cloud. It also keeps a verifiable count of each user’s system usage of each of the various components of an OpenStack cloud. Think metering and usage reporting.

Heat is the orchestration component of OpenStack, which allows developers to store the requirements of a cloud application in a file that defines what resources are necessary for that application. In this way, it helps to manage the infrastructure needed for a cloud service to run.

#### Conclusão: A arquitetura em núvem permite diminuir o disperdício de hardware e ganho na mobilidade de recursos. Contudo existem sérios riscos que podem paralizar as operações de uma empresa. Todo equipamento e arquiteturas complexas são passíveis de falhas tanto operacionais quanto de segurança. Como seria possível mitigar esses riscos?
