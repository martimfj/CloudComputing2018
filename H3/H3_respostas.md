# Cap. 3 - Private Cloud Stack - 4 Aulas
### Hugo Mendes e Martim José
## Instalando Canonical Distro:
Para baixar o charm do Openstack:
* wget https://api.jujucharms.com/charmstore/v5/openstack-base/archive
* unzip archive

Escolhemos começar um controller novo:
* `juju kill-controller`
* `juju bootstrap maas main --to juju`

Instalando o bundle customizado:
* `juju deploy bundle.yaml`

Instalando o client do Openstack:
* `sudo apt  install python3-openstackclient`

Para criar as variáveis do ambiente:
* `cd ~openstack` (diretório onde o charm foi extraído)
* `source openrc`
* `env` (imprime as variáveis de ambiente)

#### 1. Faça um desenho de como é a sua arquitetura de solução, destacando o hardware, sistema operacional/container e respectivas alocações dos serviços.


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

Para configurar uma rede "externa" e roteador compartilhado:
```
./neutron-ext-net-ksv3 --network-type flat
    -g 192.168.0.1 -c 192.168.0.0/20 \
    -f 192.168.8.1:192.168.9.254 ext_net
```

Para configurar a rede interna:
```
./neutron-tenant-net-ksv3 -p admin -r provider-router \
    [-N <dns-server>] internal 192.169.0.0/24
```

#### 2. Faça um desenho de como é a sua arquitetura de rede, desde a conexão com o Insper até a instância alocada.


## Criando usuários
#### 3. Monte um passo a passo de configuração de rede via Horizon.


## Protótipo II
* Hugo Mendes: (repositório git)
* Martim José: (repositório git)


## Deja-vu (Juju Reborn)
#### 4. Escreva as configurações utilizadas para incluir o Openstack como Cloud Provider no Juju.

#### 5. Escreva o comando de bootstrap.


## Escalando o Kubernetes
#### 6. O que é um Hypervisor? Qual o hypervisor do Openstack, da AWS e da Azure?


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

#### 3. Quais são os principais componentes dentro do Openstack? Descreva brevemente suas funcionalidades.

#### Conclusão: A arquitetura em núvem permite diminuir o disperdício de hardware e ganho na mobilidade de recursos. Contudo existem sérios riscos que podem paralizar as operações de uma empresa. Todo equipamento e arquiteturas complexas são passíveis de falhas tanto operacionais quanto de segurança. Como seria possível mitigar esses riscos?
