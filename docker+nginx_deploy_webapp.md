# Instância

1. Crie a instancia:
Imagem: Amazon Linux
Storage: 8gb ta bom pra iniciar
Key par: Cê q manda!
Este processo é simples. Crie a EC2 e o security group apenas

portas 80, 22 e 443

2. Conecte via SSH

3. instale o docker:

        sudo dnf update -y
        sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
        sudo vi /etc/yum.repos.d/docker-ce.repo

Substitua todos os baseurl:

OLD

    baseurl=https://download.docker.com/linux/fedora/$releasever/$basearch/stable

NEW

    baseurl=https://download.docker.com/linux/fedora/38/$basearch/stable

Instale os plugins e habilite o docker

    sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
    sudo systemctl enable --now docker
    sudo systemctl status docker
    sudo usermod -aG docker ec2-user
    newgrp docker

    docker run hello-world

    docker ps
    docker compose version


# Dominio

Crie uma zona hospedada no Route 53

registre os NS (Name Servers) no provedor do domínio e aguarde propagar

após isso configure o roteamento do IP para o dominio com roteamento TIPO A

# SLL

    sudo dnf install python3 python3-pip -y
    sudo pip install certbot
    certbot --version
    sudo pip install certbot-nginx
    sudo certbot certonly --standalone -d seu-dominio.com -d www.seu-dominio.com

    sudo crontab -e

0 1 * * * docker stop rtsystem-web-1 && /usr/local/bin/certbot renew --quiet && docker start rtsystem-web-1
