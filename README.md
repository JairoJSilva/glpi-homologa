# 📦 GLPI com Docker Compose — Guia de Instalação e Implantação

Este guia descreve como instalar e implantar o **GLPI** utilizando Docker Compose, de forma simples e replicável em qualquer ambiente.

---

## 📋 Pré-requisitos

Antes de iniciar, certifique-se de que o ambiente possui:

- Docker instalado (>= 20.x)
- Docker Compose instalado (>= 2.x)
- Porta **80** disponível no host
- Sistema operacional Linux (recomendado)

Verifique:

```bash
docker --version
docker compose version


📁 Estrutura de Diretórios

Organize seu projeto da seguinte forma:

glpi/
├── docker-compose.yml
├── .env
└── storage/
    ├── glpi/
    └── mysql/

🐳 Docker Compose

Crie o arquivo docker-compose.yml:

services:
  glpi:
    image: "glpi/glpi:latest"
    restart: "unless-stopped"
    volumes:
      - "./storage/glpi:/var/glpi:rw"
    env_file: .env
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "80:80"

  db:
    image: "mysql"
    restart: "unless-stopped"
    volumes:
       - "./storage/mysql:/var/lib/mysql"
    environment:
      MYSQL_RANDOM_ROOT_PASSWORD: "yes"
      MYSQL_DATABASE: ${GLPI_DB_NAME}
      MYSQL_USER: ${GLPI_DB_USER}
      MYSQL_PASSWORD: ${GLPI_DB_PASSWORD}
    healthcheck:
      test: mysqladmin ping -h 127.0.0.1 -u $$MYSQL_USER --password=$$MYSQL_PASSWORD
      start_period: 5s
      interval: 5s
      timeout: 5s
      retries: 10
    expose:
      - "3306"
🚀 Subindo o Ambiente

Execute o comando abaixo na raiz do projeto:

docker compose up -d

Verifique se os containers estão rodando:

docker ps
🌐 Acesso ao GLPI

Após a inicialização, acesse:

http://SEU_IP/

Exemplo:

http://localhost/
🧩 Instalação Inicial do GLPI
Selecione o idioma
Aceite os termos
Configure o banco de dados:
Servidor: db
Usuário: definido no .env
Senha: definida no .env
Banco: definido no .env
Finalize a instalação
🔐 Credenciais padrão

Após instalação:

Usuário: glpi
Senha: glpi

⚠️ Altere imediatamente em produção

💾 Persistência de Dados

Os dados são armazenados localmente:

GLPI: ./storage/glpi
MySQL: ./storage/mysql

Isso garante que os dados não sejam perdidos ao reiniciar os containers.

🔄 Atualização

Para atualizar as imagens:

docker compose pull
docker compose up -d
🛑 Parar o Ambiente
docker compose down
🧹 Limpeza Completa (CUIDADO)

Remove containers e dados:

docker compose down -v
rm -rf storage/
