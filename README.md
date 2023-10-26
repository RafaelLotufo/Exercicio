# Exercicio
Foi realizada uma tentativa de criação de um ambiente AWS t2-medium usando Docker Compose, configurando todos os componentes solicitados, como Zabbix, Grafana, Nginx, por meio de um arquivo YAML ao iniciar o ambiente.

yaml

version: '3'
 
services:
  zabbix-server:
    image: zabbix/zabbix-server-pgsql:latest
    container_name: zabbix-server
    ports:
      - "10051:10051"
    environment:
      - DB_SERVER_HOST=zabbix-db
      - POSTGRES_USER=zabbix
      - POSTGRES_PASSWORD=zabbix
      - POSTGRES_DB=zabbix
 
  zabbix-web:
    image: zabbix/zabbix-web-nginx-pgsql:latest
    container_name: zabbix-web
    ports:
      - "8080:8080"
    environment:
      - DB_SERVER_HOST=zabbix-db
      - POSTGRES_USER=zabbix
      - POSTGRES_PASSWORD=zabbix
      - POSTGRES_DB=zabbix
      - ZBX_SERVER_HOST=zabbix-server
 
  zabbix-db:
    image: postgres:latest
    container_name: zabbix-db
    environment:
      - POSTGRES_USER=zabbix
      - POSTGRES_PASSWORD=zabbix
      - POSTGRES_DB=zabbix
 
  grafana:
    image: grafana/grafana:latest
    container_name: grafana
    ports:
      - "3000:3000"
    environment:
      - GF_SECURITY_ADMIN_PASSWORD=admin
    depends_on:
      - zabbix-server

Infelizmente, ocorreram erros ao iniciar o banco, mas as correções foram aplicadas após encontrar o Zabbix Server. Mesmo assim, optou-se por uma nova instalação com abordagens diferentes.
Nova Criação do Ambiente Localmente

Uma nova criação do ambiente foi realizada localmente, incluindo o Zabbix Server e outros serviços em contêineres Docker. A seguir estão os passos necessários:

    Criar as pastas necessárias:

    bash

cd /srv
mkdir mysql nginx grafana

Criar o arquivo docker-compose.yaml:

bash

vi /srv/docker-compose.yaml

yaml

version: '3.8'
services:
  grafana:
    image: grafana/grafana-enterprise
    container_name: grafana
    restart: unless-stopped
    user: '0'
    ports:
      - '3000:3000'
    volumes:
      - '/srv/grafana:/var/lib/grafana'
  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_USER: 'user'
      MYSQL_PASSWORD: 'password'
      MYSQL_ROOT_PASSWORD: 'password'
    ports:
      - '3306:3306'
    expose:
      - '3306'
    volumes:
      - /srv/mysql:/var/lib/mysql
  nginx:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - /srv/nginx:/usr/share/nginx/html
    links:
      - php

Subir o ambiente:

bash

docker compose up -d

Conectar ao contêiner MySQL:

bash

    docker exec -it db mysql -uuser -p

    Adicionar páginas HTML conforme necessário.

Nota Importante: Certifique-se de monitorar logs e mensagens de erro durante a inicialização do ambiente para solucionar quaisquer problemas que possam surgir.
