## CONFIGURAÇÃO SAMBA

## 1º Passo - Faça o update primeiro, e em seguida a instalação do samba

````
sudo apt update
````
![11](https://github.com/MNahVR/Sred/blob/main/Galeria/11.png)

````
sudo apt install samba
````

![12](https://github.com/MNahVR/Sred/blob/main/Galeria/12.png)

## 2º Passo - Verifique se o samba esta indo
````
ou,
whereis samba

ou,
sudo systemctl status smbd

ou,
netstat -an | grep LISTEN
````
![13](https://github.com/MNahVR/Sred/blob/main/Galeria/13.png)

## 3º Passo - Fazer backup do arquivo de configuração do samba

````
sudo cp /etc/samba/smb.conf{,.backup}
````

## 4º Passo - Verificar se foi feito mesmo o backup do arquivo e criar um arquivo novo somente com os comandos necessários

````
ls -la /etc/samba
````

````
sudo bash -c 'grep -v -E "^#|^;" /etc/samba/smb.conf.backup | grep . > /etc/samba/smb.conf'
````

````
sudo nano /etc/samba/smb.conf
````
![14](https://github.com/MNahVR/Sred/blob/main/Galeria/14.png)

## 5º passo - Editar arquivo digitado anteriormente (/etc/samba/smb.conf)

### Adicionar o que esta escrito abaixo no diretorio [global]:

````
interfaces = 127.0.0.1/8 ens160 ens192
````

### Adicionar também a parte do diretório [publico] no final do arquivo
* Para que assim o acesso seja somente a usuários do grupo sambashare

````
[public]
   comment = public anonymous access
   path = /samba/public
   browsable =yes
   create mask = 0660
   directory mask = 0771
   writable = yes
   guest ok = no
   valid users = @sambashare
````
![15](https://github.com/MNahVR/Sred/blob/main/Galeria/15.png)

### Depois de editado o arquivo, é necessário reniciar o serviço smbd

````
sudo systemctl restart smbd
````

## 6º passo - Criar um usuário do S.O, onde vai utilizar o compartilhamento samba

````
Dados do usuário:
- usuário: aluno
- senha: adminifal
````

````
Para criar, digite:
sudo adduser aluno
````
* Vai pedir além do nome de usuario e a senha outros dados, mas não é necessário preenchê-los, no meu caso eu não preechi.

![16](https://github.com/MNahVR/Sred/blob/main/Galeria/16.png)

#### Agora é necessário vincular o usuário do S.O. ao Serviço Samba
````
* Com o camando abaixo, repita a senha de aluno ou crie uma senha nova somente para acessar o compartilhamento de arquivo

sudo smbpasswd -a aluno

![17](https://github.com/MNahVR/Sred/blob/main/Galeria/17.png)

*Depois digite:
sudo usermod -aG sambashare aluno
````
![18](https://github.com/MNahVR/Sred/blob/main/Galeria/18.png)

## 7º Passo - O samba está instalado, mas para compartilhá-lo em rede é preciso criar um diretório

````
sudo mkdir /home/<aluno>/sambashare/
````

````
sudo mkdir -p /samba/public
````
![19](https://github.com/MNahVR/Sred/blob/main/Galeria/19.png)

## 8º Passo - Para que qualquer um possa acessar o compartilhamento público, configure as permissões

````
sudo chown -R nobody:nogroup /samba/public
````
````
sudo chmod -R 0775 /samba/public
````
````
sudo chgrp sambashare /samba/public
````
![20](https://github.com/MNahVR/Sred/blob/main/Galeria/20.png)

## 9º passo - Verificar o acesso ao recurso compartilhado 

````
* Digite no Windows Explorer o endereço IP do servidor samba, com o "\\" antes
EX: \\10.9.14.221
````
![21](https://github.com/MNahVR/Sred/blob/main/Galeria/21.png)
![22](https://github.com/MNahVR/Sred/blob/main/Galeria/22.png)
![23](https://github.com/MNahVR/Sred/blob/main/Galeria/23.png)