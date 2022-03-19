# 2ª PARTE DO PROJETO FINAL DE SRED

## 1. Introdução
### Nessa etapa, devesse fornecer:

a) A apresentação das tabelas de definições de nomes e IPs para todas as VMs incluindo as novas VMs para Web e Bd
	
b) Criar as entradas no DNS Master para as VMs Web e Bd
	
c) Configurar o servidor Web (LAMP)


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


## 3.1 Nesta maquina "www" 
### vamos configurar o servidor web usando a ferramenta Apache2 e configurar a maquina como cliente do serviço DNS. 

## 1º passo:
### Mudar o nome da máquina

  ```
  $ sudo hostnamectl set-hostname www.grupo5.turma914.ifalara.local
  ```

## 2º passo - Configurar máquina como cliente do serviço DNS.
### Para isso tem que configurar as interfaces de rede e adicionar os IPs de DNS e o campo "search".

  ```
  $ sudo nano /etc/netplan/00-installer-config.yaml
  ```
### ANTES:

![1](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/1.png)

### DEPOIS:

![2](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/2.png)

### Em seguida tem que aplicar as alterações.

  ```
  $ sudo netplan apply
  ```
![3](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/3.png)

## 3º passo - Agora vamos testar se está funcionando normalmente.

  ```
  $ dig @10.9.14.126 gw.grupo4.turma914.ifalara.local
  ```
  ![4](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/4.png)

  Na linha "ANSWER SECTION:" tem que aparecer que foi resolvido, com o dominio e o IP.

  ### Verificar se a interface 160 está funcionando:
  
  ```
  $ systemd-resolve --status ens160
  ```

  ### Ver se o serviço DNS consegue revolver o DNS do google:
  
  ```
  $ ping google.com
  ```
![5](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/5.png)

## 4º passo - Agora vomos instalar o apache e configurar para subir o servidor apacha.

  ### Primeiro, fazer update na máquina:
  
  ```
  $ sudo apt update
  ```
  ![6](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/6.png)
  ### Instalar o apache2:

  ```
  $ sudo apt install apache2
  ```
  ![7](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/7.png)

## 5º passo - Verificar se o servidor web está funcionando

  ```
  $ sudo systemctl status apache2
  ```
  ![8](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/8.png)

  ### Acessar o servidor web:
  Ir no navegador e colocar o IP da maquina "www"

  ### Caso não saiba seu IP basta digitar:
  
  ```
  $ hostname -i
  ```
  
  OBS.: Se o servidor web estiver rodando aparecerá, no navegador mostrará uma pagina do Apache2.
  
  ![9](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/9.png)

## 6º passo - criar o site.

  ### Primeiro tem que criar um diretório com o nome do nosso domínio, para isso usamos:
  
  ```
  $ sudo mkdir /var/www/grupo5.turma914.ifalara.local
  ```
  
  ### Atribuir a propriedade do diretório com a variável de ambiente $USER:
  
  ```
  $ sudo chown -R $USER:$USER /var/www/grupo.turma914.ifalara.local
  ```
  
  ### Configurar as permições dos webs hosts, com:
  
  ```
  $ sudo chmod -R 755 /var/www/grupo5.turma914.ifalara.local
  ```
  ![10](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/10.png)
  
  ### Para que o Apache apresente a nossa página vamos copiar um arquivo de configuração:
  
  ```
  $ sudo nano /etc/apache2/sites-available/www.grupo5.turma914.ifalara.local.conf
  ```
  ### Adicione as informações, como mostra a imagem:
  
  ![11](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/11.png)

  ### Habilitar o arquivo com a ferramenta a2ensite:
  
  ```
  $ sudo a2ensite www.grupo5.turma914.ifalara.local.conf
  ```
  ![12](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/12.png)

  ### Desabilitar o site padrão definido em 000-default.conf:
  
  ```
  $ sudo a2dissite 000-default.conf
  ```
  ![13](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/13.png)

  ### Procura de erros de configuração:
  
  ```
  $ sudo apache2ctl configtest
  ```
  ![14](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/14.png)

  ### Vamos modificar as permissões de dono da pasta "DocumentRoot" para "www-data", que é o usuário do apache.

  ```
  $ chown -R www-data:www-data www.grupo5.turma914.ifalara.local
  ```

  ### Acessar o navegador com o IP da maquina. 

  ### Criar página WEB  em HTML
  ````
  sudo nano /var/www/grupo5.turma914.ifalara.local/index.html
  ````
  ![19](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/19.png)

  ### Reiniciar o apache
  ````
  sudo systemctl restart apache2
  ````
  ![15](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/15.png)

  ### Digitar no navegador, o ip da maquina www, para ver a página html
  
  ![16](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/16.png)

## 7º passo - Instalação PHP

  ### Vamos fazer um update na maquina:

  ```
  $ sudo apt update -y
  ```

  ### Caso precise faça o upgrade na maquina:

  ```
  $ sudo apt upgrade -y
  ```
  ### Instale o PHP

  ```
  $ sudo apt install php7.4 libapache2-mod-php7.4 php7.4-mysql php-common php7.4-cli php7.4-common php7.4-common php7.4-json php7.4-opcache php7.4-readline
  ```
  ![17](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/17.png)

  ### Carregue o php
  ```
  $ sudo a2enmod php7.4
  ```
  ![18](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/www/Galeria/18.png)

  ### E reinicie o apache2

  ```
  $ sudo systemctl restart apache2
  ```

  Obs.: O php já está funcionando

## 3.2 - Na máquina "bd" devesse configurar o servidor de banco de dados usando a ferramenta MySQL e configurar a maquina como cliente do serviço DNS. 

## 1º passo - Mudar o nome da máquina:

```
$ sudo hostnamectl set-hostname bd.grupo4.turma914.ifalara.local
```

### Em seguida reiniciar a máquina:

```
sudo reboot
``` 
![1](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/1.png)

## 2º passo- Configurar essa maquina como cliente do serviço DNS. 

### Para isso devesse configurar as interfaces de rede e adicionar os IPs de DNS e o compo "search".
Vamos entrar no arquivo de configuração:
````
$ sudo nano /etc/netplan/00-installer-config.yaml
````
![3](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/3.png)

### Aplicar as alterações.

```
$ sudo netplan apply
```
![2](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/2.png)

## 3º passo - Testar

```
$ dig @10.9.14.126 gw.grupo5.turma914.ifalara.local
```
![4](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/4.png)

Na linha "ANSWER SECTION:" tem que aparecer que foi resolvido, com o dominio e o IP.

### Verificar se a interface 160 está funcionando:

```
$ systemd-resolve --status ens160
```
![5](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/5.png)

### Ver se o serviço DNS resolve o DNS do google:

```
$ ping google.com
```
![6](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/6.png)

## 4º passo - Instalação do Mysql Server

### 4.1º passo - Fazer um update na maquina:

```
$ sudo apt update -y
```
![7](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/7.png)

Caso precise faça o upgrade na maquina:

```
$ sudo apt upgrade -y
```
![8](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/8.png)

### 4.2º passo - Instalar:

```
$ sudo apt install mysql-server
```
![9](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/9.png)

### 4.3º passo - Verificar se o serviço está funcionando:

```
$ systemctl status mysql
```
![10](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/10.png)

### 4.4º passo - Configurar

* Primeiro têm que verificar quais as portas que estão sendo utilizadas e em quais IPs:

```
$  netstat -an | grep LISTEN
```
![11](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/11.png)

* Vamos libera as portas "3306" e "33060" estão recebendo somente conexão da interface "127.0.0.1"

```
$ sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
![12](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/12.png)

* Nas linhas "mysqlx-bind-addresse" e "bind-address" vamos substituir "127.0.0.1" por "0.0.0.0"


* Reiniciar o serviço:

```
$ systemctl restart mysql
```
![13](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/13.png)

* Verificar novamente as portas

```
$ netstat -an | grep LISTEN
```
![14](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/14.png)

### 4.5º passo - Configuração de segurança

* Adicionar senha para o MySQL:

```
$ sudo mysql_secure_installation
```

* Aperte y, que é "yes"(sim), para aceitar criar a senha
* Apertar 2, para que ela seja validada como forte
* Digite e repita a senha

OBS.: A senha utilizada (sugestão do professor): "S3nh4@ifal"

* Apertar y, y, y, n, y. Na ordem, para que seja configurado quem pode acessar e etc. 

![15](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/15.png)

![16](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/16.png)

### 4.6º passo - Iniciar MySQL

```
$ sudo mysql
```

OBS.: Para sair tem que digitar: exit

![17](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/17.png)

### 4.7º passo - - Trabalhando com MySQL

* Criar o Banco de Dados "projetofinal_sred" com as tabelas "grupo", "aluno" e "host"

* Para criar o banco de dados, utilizou-se como referência o arquivo "createdb.sql", disponibilizado pelo professor :

[link_createdb.sql](https://github.com/alaelson/labredes2021/blob/main/network/lamp/install/createdatabase.md)

* Nosso database:
````
-- Cria o database do projeto final.
CREATE DATABASE projetofinal_sred;
-- Acessa a database do projeto final
use projetofinal_sred;
-- Cria as tabelas aluno, grupo e host
CREATE TABLE aluno (UID int(10) unsigned NOT NULL AUTO_INCREMENT, Nome varchar (255), Email varchar (255), GID int,PRIMARY KEY (`UID`)) ENGINE=InnoDB DEFAULT CHARSET=utf8;
CREATE TABLE grupo (GID int(10) unsigned NOT NULL, Nome varchar (255), Dominio varchar (255), PRIMARY KEY (`GID`)) ENGINE=InnoDB DEFAULT CHARSET=utf8;
CREATE TABLE host (HID int(10) unsigned NOT NULL AUTO_INCREMENT, VmName varchar (255), FQDName varchar (255), GID int, PRIMARY KEY (`HID`)) ENGINE=InnoDB DEFAULT CHARSET=utf8;
-- mostra as tabelas e os registros armazenados em cada tabela
SHOW TABLES;
SELECT * FROM aluno;
SELECT * FROM grupo;
SELECT * FROM host;
-- cria o usuario um com acesso pelo localhost: user: 'pfadmin' e senha '4dm1n@BD'
CREATE USER 'pfadmin'@'localhost' IDENTIFIED BY '4dm1n@BD';
-- concede privilegios de acesso a todas as tabelas do mysql ao usuario 'pfadmin'
GRANT ALL PRIVILEGES ON *.* TO 'pfadmin'@'localhost' WITH GRANT OPTION;

-- cria o usuario um com acesso pelo servidor web: user: 'pfadmin' e senha '4dm1n@BD'. Então coloque o IP do servidor web
CREATE USER 'pfadmin'@'10.9.14.121' IDENTIFIED BY '4dm1n@BD';
-- concede privilegios de acesso a todas as tabelas do mysql ao usuario 'pfadmin' logando remotamente pelo ip 10.9.14.221.
-- Use o IP do servidor Web do seu grupo
GRANT ALL PRIVILEGES ON *.* TO 'pfadmin'@'10.9.14.221' WITH GRANT OPTION;
exit
````

## 5º Passo - Testagem do banco de dados

![18](https://github.com/MNahVR/Sred-Final/blob/main/2Etapa/bd/Galeria/18.png)