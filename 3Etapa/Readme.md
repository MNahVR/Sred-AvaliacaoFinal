# 3ª PARTE DO PROJETO FINAL DE SRED

## 1. Introdução
### Nessa etapa, devesse fornecer:

a) A apresentação das tabelas de definições de nomes e IPs para todas as VMs incluindo as novas VMs para Web e Bd
	
b) A implementação o GW: realizar a configuração do arquivo /etc/rc.local que reconfigura o firewall IPTABLES para redirecionar pacotes da rede interna para a rede externa.
	
c) A realização do port forwarding no rc.local para encaminhar dados para as portas 80 ( servidor Web), 445 e 139 (Samba) 
	
d) Reconfigurar as interfaces do SAMBA, WWW e BD para que passem somente pelo GW.


## 2. Definições iniciais:

 |          GRUPO 5					           |
 |:------------------------------------|
 |         Nome dos integrantes        |
 |Aluno 1:   Maria Nathálya Veiga Rodrigues  |
 |Aluno 2:	 Diogo Bonfim Pinheiro	      |
 |Aluno 3:	 Sandrielly Amorim Silva      |


### CONFIGURAÇÕES DAS INTEFACES DE REDE		
|IP da Subrede:| 10.9.14.0/24  | 	192.168.14.32/29 |
|:-------------|:------------  | :---------------- |
|IP de Broadcast:|	10.9.14.255/24	|	192.168.14.39|		
|IP do GW:|	ens160 10.9.14.115	|ens192 	192.168.14.33|
|IP do SAMBA:	|ens160	10.9.14.104	| ens192	192.168.14.34|
|IP do NS1:	|ens160	10.9.14.121	| ens192	192.168.14.35|
|IP do NS2:	|ens160	10.9.14.129	| ens192	192.168.14.36|
|IP do WEB	|ens160	10.9.14.221	| ens192	192.168.14.37|
|IP do BD	|ens160	10.9.14.222	| ens192	192.168.14.38|
					

### Definição de Nomes e Domínio (<grupo>.<turma>.ifalara.local):		
|VM	Domínio | (zona): |grupo5.turma914.ifalara.local|
|:----------|:--------|:----------------------------|			
|Aluno29	|FQDN do GW:	|gw.grupo5.turma914.ifalara.local|
|Aluno04	|FQDN do SAMBA:	|smb.grupo5.turma914.ifalara.local|
|Aluno21	| FQDN do NS1:	| ns1.grupo5.turma914.ifalara.local|
|Aluno15 |	FQDN do NS2: | ns2.grupo5.turma914.ifalara.local|
|Grupo5vm1 | FQDN do WEB	| www.grupo5.turma914.ifalara.local |
|Grupo5vm2	| FQDN do BD	| bd.grupo5.turma914.ifalara.local |

## 3. Implementação dos Serivços de Rede

## 3.1 Configuração da máquina "smb"
### Nessa máquina devesse adicionar a resolução de nomes e o serviço de compartilhamento de arquivos SAMBA.

## 1º Passo - Mudar nome da máquina

```
$ sudo hostnamectl set-hostname smb.grupo4.turma914.ifalara.local
```


## 2º Passo - Configurar máquina como cliente do serviço DNS. 
### Para isso devesse configurar as interfaces de rede e adicionar os IPs de DNS e o compo "search".

```
$ sudo nano /etc/netplan/00-install-config.yaml
```

## Aplicar alterações.

```
$ sudo netplan apply
```


## 3º Passo - Testar o funcionando:

```
$ dig @10.9.14.121 gw.grupo5.turma914.ifalara.local
```
Na linha "ANSWER SECTION:" tem que aparecer que foi resolvido, com o dominio e o IP.

### Verificar se a interface 160 está funcionando:
```
$ systemd-resolve --status ens160
```

### Para finalizar vamos devesse ver se o nosso serviço DNS resolve o DNS do google:
````
$ ping google.com
````

## 4º Passo - Instalar o Samba

### Primeiro dar um update na máquina

```
$ sudo apt update
```
### Instalar:
```
$ sudo apt install samba 
```
### Ver status do samba:

```
$  sudo systemctl status smbd
```
