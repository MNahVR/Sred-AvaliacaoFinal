# Nesta máquina ns1 vamos configurar o serviço de resolução de nome (DNS) com o Bind9, utilizando-a como DNS Master. 

## 1º passo - Tem que mudar o nome da maquina para o nome escolhido, e que está na tabela:
### ns1.grupo5.turma914.ifalara.local

### Com o comando:
```
$ sudo hostnamectl set-hostname ns1.grupo5.turma914.ifalara.local

Agora vamos reiniciar a maquina para atualizar o nome:

$ sudo reboot
````

## 2º passo - Instalação do bind9.
Antes de intalar o bind9, iremos fazer um update:
````
$ sudo apt update
````

Agora vamos instalar o bind:
````

$ sudo apt-get install bind9 dnsutils bind9-doc
````
Em seguida vamos verificar se o Bind9 está funcionando:
````

$ sudo systemctl status bind9
````

## 3º passo - Configuração dos arquivos de zonas.

Antes deve-se criar o diretorio zones para alocar os nossos arquivos de zonas, com: 
````
$ sudo mkdir /etc/bind/zones
````
Agora vamos copiar os arquivos para a pasta zones.

Zona Direta:
````
$ sudo cp /etc/bind/db.empty /etc/bind/zones/db.grupo5.turma914.ifalara.local
Obs.: o nosso grupo (grupo 5),  optou por ter o domínio como: turma914.ifalara.local 
````

Zona Reversa:
````
$  sudo cp /etc/bind/db.127 /etc/bind/zones/db.10.9.14.rev
Obs.: o espaço de rede é 10.9.14
````

Agora é que vamos editar o arquivo "db.grupo5.turma914.ifalara.local":
````
$ sudo nano /etc/bind/zones/db.grupo5.turma914.ifalara.local
````
Nele vamos colocar os DNS e os IPs:

RESULTADO:
````
;
; BIND data file for internal network
;
$ORIGIN grupo5.turma914.ifalara.local.
$TTL	3h
@	IN	SOA	ns1.grupo5.turma914.ifalara.local. root.grupo4.turma914.ifalara.local. (
			      2022030900		; Serial
			      3h	; Refresh
			      1h	; Retry
			      1w	; Expire
			      1h )	; Negative Cache TTL
;nameservers
@	IN	NS	ns1.grupo5.turma914.ifalara.local.
@	IN	NS	ns2.grupo5.turma914.ifalara.local.

;hosts
ns1.grupo5.turma914.ifalara.local.	  IN	A	10.9.14.121
ns2.grupo5.turma914.ifalara.local.	  IN	A	10.9.14.129
smb.grupo5.turma914.ifalara.local.	  IN	A	10.9.14.104
gw.grupo5.turma914.ifalara.local.	  IN 	A	10.9.14.115
www.grupo5.turma914.ifalara.local.	  IN 	A	10.9.14.221
bd.grupo5.turma914.ifalara.local.	  IN 	A	10.9.14.222
````
![5](https://github.com/MNahVR/Sred-Final/tree/main/1Etapa/Galeria/zonaDireta.PNG)

E vamos editar também o arquivo "db.10.9.14.rev":
````
$ sudo nano /etc/bind/zones/db.10.9.14.rev
````
Nele vamos colocar os DNS e os IPs:

RESULTADO:
````
;
; BIND reverse data file of reverse zone for local area network 10.9.14.0/24
;
$TTL    604800
@       IN      SOA     grupo5.turma914.ifalara.local. root.grupo5.turma914.ifalara.local. (
                              2022030900         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL

; name servers
@      IN      NS      ns1.grupo5.turma914.ifalara.local.
@      IN      NS      ns2.grupo5.turma914.ifalara.local.

; PTR Records
121   IN      PTR     ns1.grupo5.turma914.ifalara.local.
129   IN      PTR     ns2.grupo5.turma914.ifalara.local.
115   IN      PTR     gw.grupo5.turma914.ifalara.local.
104   IN      PTR     smb.grupo5.turma914.ifalara.local.
221   IN      PTR     www.grupo5.turma914.ifalara.local.
222   IN      PTR     bd.grupo5.turma914.ifalara.local.
````
![6](https://github.com/MNahVR/Sred-Final/tree/main/1Etapa/Galeria/zonaReversa.PNG)

## 4º passo - Ativação das zonas

### Para isso, devesse configurar o arquivo "named.conf.local":
````
$ sudo nano /etc/bind/named.conf.local
````
RESULTADO:
````
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

Nele vamos adicionas as seguintes linhas de codigos logo a baixo, pulando uma linha:

zone "grupo5.turma914.ifalara.local" {
	type master;
	file "/etc/bind/zones/db.grupo5.turma914.ifalara.local";
	allow-transfer{ 10.9.14.129; };  
	allow-query{any;};
};

zone "14.9.10.in-addr.arpa" IN {
	type master;
	file "/etc/bind/zones/db.10.9.14.rev";
	allow-transfer{ 10.9.14.129; };
};
````
Em zones tem que ter o nome de dominio escolhido. E em "allow-transfer", tem que colocar o IP é do ns2, ou seja Slave.

![4](https://github.com/MNahVR/Sred-Final/tree/main/1Etapa/Galeria/namedConfLocal.PNG)


## 5º passo - Verificar a sintax dos arquivos que editamos.

### Para checar a sintax do arquivo named.conf.local usamos:
````
$ sudo named-checkconf
````

### Agora vamos entrar no diretório zones:
````
$ cd /etc/bind/zones
````
### Para verificar se o arquivo "db.grupo5.turma914.ifalara.local" está ok. Digite:
````
$ sudo named-checkzone grupo5.turma914.ifalara.local db.grupo5.turma914.ifalara.local
````
RESULTADO:
````
zone grupo5.turma914.ifalara.local/IN: loaded serial 1
OK
````
Vamos verificar se o arquivo "db.10.9.14.rev" está ok também. Digite:
````
$ sudo named-checkzone 14.9.10.in-addr.arpa db.10.9.14.rev
````
RESULTADO:
````	
zone 14.9.10.in-addr.arpa/IN: loaded serial 1
OK
````
![2](https://github.com/MNahVR/Sred-Final/tree/main/1Etapa/Galeria/chack.PNG)


## 6º passo - Configuração para resolver apenas IPv4.

### Primeiro, temos que entrar no arquivo name e adicionaremos "-4" na linha "OPTINS=-u bind":
````
$ sudo nano /etc/default/named
````
RESULTADO:
````
# run resolvconf?
RESOLVCONF=no

# startup options for the server
OPTIONS="-4 -u bind"
````

## 7º passo - Reiniciar o bind, com:
````
$ sudo systemctl restart bind9
````
## 8º passo - Configuração das nossas interfaces, ens160 e ens192:
````
$ sudo nano /etc/netplan/00-installer-config.yaml
````
### Na interface ens160 devesse retirar os endereços de IPs e adiconar os indereçoes do Master e Sleve.

RESULTADO:
````
network:
  ethernets:
    ens160:
      addresses: [10.9.14.121/24]
      gateway4: 10.9.14.1
      dhcp4: false
      nameservers:
        addresses:
          - 10.9.14.121
          - 10.9.14.115
        search: [grupo5.turma914.ifalara.local]
    ens192:
      addresses: [192.168.14.35/29]
  version: 2
````
![1](https://github.com/MNahVR/Sred-Final/tree/main/1Etapa/Galeria/00_installer_config.PNG)

## 9º passo - Testar o DNS Marter.
````
$ dig @10.9.14.129 gw.grupo5.turma914.ifalara.local
````
OBS.: Na linha "ANSWER SECTION:" tem que mostra que foi resolvido, com o dominio e o IP.

### Para verificar se a interface 160 está funcionando, digite:
````
$ systemd-resolve --status ens160
````
### Para finalizar vamos ver se o nosso serviço DNS revolve o DNS do google:
````
$ ping google.com
````
![3](https://github.com/MNahVR/Sred-Final/tree/main/1Etapa/Galeria/dig-system_resolve-ping.PNG)
