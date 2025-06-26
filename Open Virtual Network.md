Nesta seção iremos abordar o setup do Open Virtual Network (OVN) para o nosso cluster Incus nas pamonhas. Com ele podemos montar redes virtuais para conectar múltiplas instâncias e fazê-las terem acesso a internet.
Com essa configuração temos algumas atividades em mente:
- ~~Criar **Ansible Playbooks** para automatizar o setup. Assim, sempre que ncessário subir o serviço de novo, tudo estará pronto.~~
- Testes de conectividade
	-  ~~Pingar VMs que estão na mesma *rede lógica*~~
	-  ~~~Ping para internet com teste de *floating IP*~~
	-  Teste de *migração* de uma VM de um compute node para outro e ver se suas portas abertas ainda funcionam normalmente.
	-  ....
- Testes com **Load Balancing**
	-  Entender como funciona a fundo load balancers. iremos usar *Linux Virtual Server* (LVS)
	-  *Implementar* LVS no roteador Curau e conseguir testar migração de fluxo entre instâncias em uma rede virtual que estão a receber pacotes
		-  É preciso configurar *DNAT com LVS*
- Usar DPDK alguma hora (futuro...)

Temos como objetivo com essas atividades aprender a implementar serviços de rede que uma cloud precisa e entender a fundo como funciona por baixo dos panos o OVN, Load Balancer, para nos capacitarmos e podermos discutir e contribuir com a comunidade.

Nas próximas seções discutiremos o porquê o OVN é tão interessante e importante para essas questões e nosso passo a passo da trajetória desse destemido camingo que é **SETAR O OVN**