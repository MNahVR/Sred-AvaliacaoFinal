# Na máquina "bd" devesse configurar o servidor de banco de dados usando a ferramenta MySQL e configurar a maquina como cliente do serviço DNS. 

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