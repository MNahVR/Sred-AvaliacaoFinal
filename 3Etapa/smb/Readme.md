# Configuração da máquina "smb", onde vai adiconar a resolução de nomes e o serviço de compartilhamento de arquivos SAMBA.

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
