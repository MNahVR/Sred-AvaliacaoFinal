# Na máquina ns2, é necessário configurar o Dns Slave com os dominios corretos

## 1º passo - Corrigir o nome da maquina, colocando de acordo com a planilha de acompanhamento
````
$ sudo hostnamectl set-hostname ns2.grupo5.turma914.ifalara.local
````
Agora vamos reiniciar a maquina para atualizar o nome:
````
$ sudo reboot
````
![1](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/1.png)

## 2º passo - Instalação do bind9.
### Antes de intalar o bind9, iremos fazer um update:
````
$ sudo apt update
````

### Agora vamos instalar o bind:

````
$ sudo apt-get install bind9 dnsutils bind9-doc
````
![2](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/2.png)

### Em seguida vamos verificar se o Bind9 está funcionando:

````
$ sudo systemctl status bind9
````

![3](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/3.png)

## 3º passo - Configuração das interfaces de redes. Para isso digite:
````
$ sudo nano /etc/netplan/00-installer-config.yaml
````
### Mudar as configurações de acordo com o que está abaixo:
````
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens160:
      addresses: [10.9.14.115/24]
      gateway4: 10.9.14.1
      dhcp4: false
      nameservers:
        addresses:
          - 10.9.14.1		
          - 10.9.14.115
        search: [grupo5.turma914.ifalara.local]
    ens192:
      addresses: [192.168.14.36/29]
  version: 2
````
![4](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/4.png)

### Para aplicar as mudanças, digite:
````
$ sudo netplan apply
````
### Verificar se está ok:
````
$ ifconfig
````
![5](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/5.png)

## 4º passo - Verificar se o Bind está rodando
````
$ sudo systemctl status bind9
````
![6](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/6.png)

### Devemos agora editar o arquivo "named.conf.local" informando que esse é o DNS Slave e qual o ip e o arquivo de DNS do Master. 

````
$ sudo nano /etc/bind/named.conf.local
````
![7](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/7.png)

### Configure, como está abaixo:
````
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";

zone "grupo5.turma914.ifalara.local" {
        type slave;
        file "/etc/bind/zones/db.grupo5.turma914.ifalara.local";
        masters { 10.9.14.121; };
};

zone "14.9.10.in-addr.arpa" IN {
        type slave;
        file "/etc/bind/zones/db.10.9.14.rev";
        masters { 10.9.14.121; };
};
````
![8](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/8.png)

### Para verificar o que foi escrito anteriormente, usasse o comando:
````
$ sudo named-checkconf
````
OBS.: Se não apareceu nem um erro então está ok.


## 5º passo - Testar para ver se está funcionando
````
$ systemd-resolve --status
````
![9](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/9.png)

E testar também se o nosso DNS está resolvendo o nome do google.
````
$ ping google.com
````
![10](https://github.com/MNahVR/Sred-Final/blob/main/1Etapa/ns2/Galeria/10.png)