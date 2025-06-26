# Introdução
As configurações para nosso cluster foram feitas com base na documentação do próprio Incus:
https://linuxcontainers.org/incus/docs/main/howto/network_ovn_setup/

Não iremos ir passo a passo, pois a documentação já faz isso. Porém, neste guia queremos mostrar a implementação em uma interface própria dedicada para o OVN e para instâncias terem conectividade com a internet.

#### Relembrando...
Nosso cluster físico é composto por 4 computadores. 
- Curau: máquina front-end com ip público conectada a internet e funciona como roteador para as 3 máquinas restantes
- Pamonha (1, 2 e 3): Compute nodes que está nosso cluster Incus. Estão conectadas com o Curau por duas redes diferentes: 192.168.35.0/24 e 10.10.35.0/24* (falaremos mais sobre essa última rede mais a frente). Cada computador tem o número final do IP com o seu número como pamonha (e.g. pamonha1 terá 192.168.35.1)
O Incus, bem como aplicações como o Linstor, se comunicam pela rede 192.168.35.0/24. Nada é conectado a outra rede.

## Implementação e Problemas
**PROBLEMA:** Seguir a documentação é rápido e objetiv todos os passos que pediam os IPs das Pamonhas, colocavamos o IP da rede dedicada (i.e 10.10.35.0/24), e mesmo tudo rodando perfeitamente, não conseguíamos criar redes ```type=ovn```.

Desta forma, chegamos a realização que o Open Virtual Network faz as interfaces dedicadas cairem. Ou seja, se no pamonha1 o interface que tinha IP ```10.10.35.1``` se chamava *enp3s0*, ela era automaticamente colocada para DOWN, pois o OVN pega a interface para si. Se quer ver como isso acontece, tente criar uma Linux Bridge e de um attach em uma interface física, verá que na hora a interface irá cair e ficar DOWN.

**SOLUÇÃO:** 
"Acabar" com a rede. Tiramos todos os IPs da rede dedicada dos pamonhas (o Curau continuou). 

Assim, em toda a documentação até o passo **4** colocamos os IPs na faixa 192.168.35.0/24. Mas agora que vem o pulo do gato

No passo **5** criamos a UPLINK, rede responsável por toda a conectividade de todas as redes do tipo OVN. Atente-se ao passo a passo:
``` sh
incus network create UPLINK --type=physical parent=<uplink_interface> /
	--target=<machine_name_1>
```
- Em todas as máquinas que você criar a rede UPLINK, você passará em **parent** a interface que conectava pela rede 10.10.35.0/24 (Neste momento não há rede, estou só especificando a interface para melhor visualizar). É desta maneira que específicamos para o OVN que é nesta interface que temos sua rede dedicada.

``` sh
incus network create UPLINK --type=physical ipv4.ovn.ranges=<IP_range>
   ipv4.gateway=<gateway>
```
* Criado a rede em todas as máquinas, é hora de defini-lá de fato:
	* ```ipv4.ovn.ranges``` passaremos o range de IPs (floating IPs) que queremos que o OVN use (perceba que estamos definindo nossa rede de novo assim). Passaremos 10.10.35.1-10.10.35.200, assim podemos ter cerca de 200 redes virtuais, já que cada rede consome um IP
	* ```ipv4.gateway``` Ta na hora de saber por onde o fluxo de dados tem que passar para chegar na internet. Lembre que eu não tirei o IP do Curau (nosso roteador e gateway), que esta na mesma faixa de IP que acabamos de setar para as redes OVN. Assim, passaremos o IP dele!

Agora é só correr pro abraço e criar uma rede teste e ver se faz ping entre duas vms e ping no *google.com*. Podemos criar uma rede OVN desta maneira:
```sh
incus network create test-ovn --type=ovn network=UPLINK ipv4.address=auto
```
Perceba que ela tanto ganha uma faixa para sua rede própria quanto também um floating IP para comunicação externa.

**IMPORTANTE:** o OVN e Incus entendem esses IPs como floating IPs, ou seja, IPs públicos. Porém, na realidade eles ainda são IPs virtuais que fazem NAT com o roteador Curau. Isso quer dizer que podemos pingar para a internet, mas um cliente ainda não consegue se comunicar com nós, pois não é IP público de fato.

## Ansible-playbook
Nosso playbook ficou dessa maneira (atente-se ao arquivo hosts.ini que já mostramos anteriormente):
```yaml
Em contrução...
```
