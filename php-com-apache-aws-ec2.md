# Domínio

Crie uma zona hospedada no route 53 e aponte os name servers no servidor DNS do domínio

Associe um IP Elático a instancia

# Banco

A gosto do freguês

# Instância

vc pode fazer o deploy com docker + nginx (meu preferido)

mas esse tutorial usa APACHE, pois meu amigo do PHP é obsoleto (ECA!) KK

1. Crie a instancia: 

- Imagem: Amazon Linux
- Storage: 8gb ta bom pra iniciar
- Key par: Cê q manda!

> Este processo é simples. Crie a EC2 e o security group apenas

portas 80, 22 e 443

2. Conecte via SSH

3. Instale o Apache, comandos:

        curl ifconfig.me
        cat /etc/system-release
        sudo dnf update -y
        sudo dnf install -y httpd php php-mysqli mariadb105
        clear
        sudo systemctl start httpd
        sudo systemctl enable httpd
        sudo usermod -a -G apache ec2-user
        exit
        groups

Aqui php e o servidor já está instalado  e o apache será exibidos após executar o último comando.

    sudo chown -R ec2-user:apache /var/www
    sudo chmod 2775 /var/www
    find /var/www -type d -exec sudo chmod 2775 {} \;
    find /var/www -type f -exec sudo chmod 0664 {} \;

Após isso vc ja deve conseguir adicionar arquivos via FTP e acessar a aplicação no IP Público da instancia, então vai lá BUNITÃO >> Faça o TESTE!

Acesse via SSH/FTP e crie o arquivo /var/www/html/index.php

    <?php
        echo "Hello World!";
    ?>

Se funcionou até aqui. Agora só falta o SSL :D

# SSL

Crie um VirtualHost na porta 80:

NOME DO DOMINIO NO EXEMPLO: MEUSITE

    sudo mkdir -p /var/www/html
    sudo chown -R ec2-user:ec2-user /var/www/html

/etc/httpd/conf.d/MEUSITE.conf

    <VirtualHost *:80>
        ServerName MEUSITE.com.br
        ServerAlias www.MEUSITE.com.br
        DocumentRoot /var/www/html

        <Directory /var/www/html>
            AllowOverride All
            Require all granted
        </Directory>

        # Só redireciona para HTTPS depois do Certbot
        # Redirect permanent / https://MEUSITE.com.br/   <-- comente por enquanto
    </VirtualHost>

Agora reinicia

    sudo systemctl restart httpd

Vamos usar o certbot, comandos:

    sudo yum install certbot python3-certbot-apache
    sudo certbot --apache
    sudo certbot --apache -d MEUSITE.com.br -d www.MEUSITE.com.br

Instalando cron:

    sudo dnf install -y cronie
    sudo systemctl enable --now crond
    systemctl status crond

Renovando Certificado automaticamente:

    sudo crontab -e

A cada segunda 3 da madruga

    0 3 * * 1 certbot renew --quiet && systemctl restart httpd

**Agora force o redirect descomentando a linhas do arquivo VirtualHost**

    sudo systemctl restart httpd
