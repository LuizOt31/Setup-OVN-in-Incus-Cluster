# First thing first: Why OVN?

Primeiramente, por que instalar o OVN e setá-lo para o Incus? A primeira vista parece que é uma etapa desnecessária, já que na própria documentação do Incus, há vários outros tipos de redes que podemos usar para nossas instâncias.

Para entendermos, vamos olhar para o console da Magalu Cloud onde nossas VMs estão:![[2025-05-28 11.21.36 console.magalu.cloud dec518ab0174.png]]  
Perceba o ipv4 público delas (172.18.0.0/16), todas elas estão em uma mesma rede. Isso quer dizer que elas podem se ver, e caso queira testar isso, entre em uma das instâncias e tente pingar alguma outra máquina para ver no que dá!

Porém, mesmo elas estando na mesma rede, não necessáriamente essas instâncias estão no mesmo computador, na mesma rede física, nem no mesmo país.

Em ambientes cloud, não queremos nos preocupar onde estão as instâncias, elas podem estar em qualquer máquina e funcionar perfeitamente bem. Então como essas máquinas virtuais podem estar se comunicando?

A resposta disso é por tuneis, uma abstração de redes para fazer máquinas em redes diferentes se entendam na mesma rede. Caso queira estudar mais a fundo sobre, estudar: VXLAN e, principalmente, Geneve.

Beleza! Sabendo disso, para isso ocorrer bem no Incus, sem precisarmos nos preocupar em fazer vários settings de bridges e tuneis em **cada** máquina (acredite, da trabalho), o OVN faz por nós. Essa é a importancia de setá-lo

## Como funciona o OVN
Na seção anterior explicamos rapidamente o porquê usar OVN e espero ter te convencido de sua importância, mas vamos abordar um pouquinho mais de como ele funciona?
....