# Nesta maquina "www" vamos configurar o servidor web usando a ferramenta Apache2 e configurar a maquina como cliente do serviço DNS. 

## 1º passo - Mudar o nome da máquina

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