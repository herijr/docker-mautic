# Docker Mautic Image

<img src="Mautic_Logo_RGB_LB.png" style="width:100%;height:auto;" />

# License

Mautic is distributed under the GPL v3 license. Full details of the license can be found in the [Mautic GitHub repository](https://github.com/mautic/mautic/blob/staging/LICENSE.txt).

# Sobre este repositório

Criei minha própria imagem do Mautic a partir do código disponibilizado no repositório oficial e subi para o Docker Hub


## Criando imagens

É possível criar as próprias imagens facilmente usando o comando docker build na raiz deste diretório:

```
docker build . -f apache/Dockerfile -t mautic/mautic:v4-apache
docker build . -f fpm/Dockerfile -t mautic/mautic:v4-fpm
```


# Puxando imagens do Docker Hub

    docker pull herijr/mautic:v4
    
# Dados persistentes

Mautic salva os dados persistentes no diretório /data, as imagens estão em /var/www/html/data/media - Você precisará anexar volumes nesses caminho para salvar os dados.

# Rodando um container básico

Criar uma Network para conectar Mautic e MySQL:

    $ docker network create mauticnet

Subindo o MySQL Server 5.7+ (Percona, MariaDB or MySQL):

    $ docker volume create mysql_data

    $ docker run --name database -d \
        --restart=always \
        -p 3306:3306 \
        -e MYSQL_ROOT_PASSWORD=mypassword \
        -v mysql_data:/var/lib/mysql \
        --net=mauticnet \
        percona/percona-server:5.7 \
         --character-set-server=utf8mb4 --collation-server=utf8mb4_general_ci

Subindo o Mautic:

    $ docker volume create mautic_data
    
    $ docker volume create mautic_images

    $ docker run --name mautic -d \
        --restart=always \
        -e MAUTIC_DB_HOST=database \
        -e MAUTIC_DB_USER=root \
        -e MAUTIC_DB_PASSWORD=mypassword \
        -e MAUTIC_DB_NAME=mautic \
        -e MAUTIC_RUN_CRON_JOBS=true \
        -p 8080:80 \
        --net=mauticnet \
        -v mautic_data:/data \
        -v mautic_images:/var/www/html/data \
        herijr/mautic:v4

Irá subir uma instalação básica em http://localhost:8080.

