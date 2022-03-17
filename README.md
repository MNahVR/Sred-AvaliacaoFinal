# Sred - Avaliação Final

Repositório destinado a avaliação final da matéria de SRED
```
1º Etapa - [ns1] - (https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns1/Readme.md) - [ns2] - (https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Readme.md)

a) Apresentar as tabelas de definições de nomes e IPs para todas as VMs

b) Criar o DNS Master e Slave, conforme o nome de domínio <grupoX>.<turma9yz>.ifala.local.
  
c) Criar a página do github do projeto do grupo.


2º Etapa: 
a) Apresentar as tabelas de definições de nomes e IPs para todas as VMs incluindo as novas VMs para Web e Bd
b) Criar as entradas no DNS Master para as VMs Web e Bd
c) Configurar o servidor Web (LAMP)


3º Etapa:
a) Apresentar as tabelas de definições de nomes e IPs para todas as VMs incluindo as novas VMs para Web e Bd
b) implementar o GW: realizar a configuração do arquivo /etc/rc.local que reconfigura o firewall IPTABLES para redirecionar pacotes da rede interna para a rede externa.
c) realizar o port forwarding no rc.local para encaminhar dados para as portas 80 ( servidor Web), 445 e 139 (Samba) 
d) Reconfigurar as interfaces do SAMBA, WWW e BD para que passem somente pelo GW.
  ````
 
 |          GRUPO 5					           |
 |:------------------------------------|
 |         Nome dos integrantes        |
 |Aluno 1:   Maria Nathálya Veiga Rodrigues  |
 |Aluno 2:	 Diogo Bonfim Pinheiro	      |
 |Aluno 3:	 Sandrielly Amorim Silva      |


## CONFIGURAÇÕES DAS INTEFACES DE REDE		
|IP da Subrede:| 10.9.14.0/24  | 	192.168.14.32/29 |
|:-------------|:------------  | :---------------- |
|IP de Broadcast:|	10.9.14.255/24	|	192.168.14.39|		
|IP do GW:|	ens160 10.9.14.115	|ens192 	192.168.14.33|
|IP do SAMBA:	|ens160	10.9.14.104	| ens192	192.168.14.34|
|IP do NS1:	|ens160	10.9.14.121	| ens192	192.168.14.35|
|IP do NS2:	|ens160	10.9.14.129	| ens192	192.168.14.36|
|IP do WEB	|ens160	10.9.14.221	| ens192	192.168.14.37|
|IP do BD	|ens160	10.9.14.222	| ens192	192.168.14.38|
					

## Definição de Nomes e Domínio (<grupo>.<turma>.ifalara.local):		
|VM	Domínio | (zona): |grupo5.turma914.ifalara.local|
|:----------|:--------|:----------------------------|			
|Aluno29	|FQDN do GW:	|gw.grupo5.turma914.ifalara.local|
|Aluno04	|FQDN do SAMBA:	|smb.grupo5.turma914.ifalara.local|
|Aluno21	| FQDN do NS1:	| ns1.grupo5.turma914.ifalara.local|
|Aluno15 |	FQDN do NS2: | ns2.grupo5.turma914.ifalara.local|
|Grupo5vm1 | FQDN do WEB	| www.grupo5.turma914.ifalara.local |
|Grupo5vm2	| FQDN do BD	| bd.grupo5.turma914.ifalara.local |
					
