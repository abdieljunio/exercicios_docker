# exercicios_docker
Lista de exercícios para fixação do conhecimento sobre docker
## 1.Rodando um container básico
Execute um container usando a imagem do Nginx e acesse a página padrão no navegador.
#### 1° Passo: Criar e acessar um diretório para a página com os comandos: 
 `mkdir meu_site`  
 `cd meu_site `  
 #### 2° Passo: Criar um arquivo index.html dentro deste diretório e coloque o conteúdo da página:  
 Exemplo:  
 ```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página Simples com Tailwind CSS</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
</head>
<body class="bg-gray-100 text-gray-800">
    <header class="bg-blue-600 p-4">
        <h1 class="text-white text-3xl text-center">Minha Página com Tailwind CSS</h1>
    </header>
    <main class="container mx-auto mt-5">
        <section class="bg-white p-6 rounded-lg shadow-md">
            <h2 class="text-2xl font-bold mb-4">Bem-vindo!</h2>
            <p class="mb-4">Esta é uma página HTML simples estilizada com Tailwind CSS.</p>
            <a href="#" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-700">Saiba Mais</a>
        </section>
    </main>
    <footer class="bg-gray-800 text-white text-center p-4 mt-5">
        <p>&copy; 2025 Meu Site. Todos os direitos reservados.</p>
    </footer>
</body>
</html>

```
#### 3° Passo: Utilizar um Dockerfile para executar a imagem oficial do Nginx, remover a pagina padrão e substituir pela página criada e exibir a página na porta 80:  
```
# Usar a imagem oficial do Nginx
FROM nginx:alpine

# Remover a página padrão de boas-vindas do Nginx
RUN rm /usr/share/nginx/html/index.html

# Copiar o arquivo index.html para o diretório padrão do Nginx
COPY index.html /usr/share/nginx/html/

# Expor a porta 80
EXPOSE 80

```
Execute os seguintes comandos:  
`docker build -t meu_site_nginx .`: para construir a imagem docker.   
`docker run -d -p 8080:80 meu_site_nginx` : para executar o container.  
#### 4º Passo: Abra o navegador e acesse `http://localhost:8080` para ver a pàgina criada.  

## 2. Criando e rodando um container interativo, inicie um container Ubuntu e interaja com o terminal dele.
#### 1º Passo: Utilize o comando `docker pull ubuntu` para baixar a imagem mais recente do Ubuntu
#### 2° Passo: Utilize o comando `docker run -it --privileged ubuntu` para acessar o container de forma iterativa e ter acesso privilegiado ao container
#### 4° Passo: Utilize os comando `apt-get update` e depois `apt-get install nano`. Para buscar versões mais recentes dos pacotes e para instalar o editor de texto nano.
#### 5º Psso: Use o coamdno `nano log_script.sh` para criar um script e utilize o seguinte conteúdo para mostrar os logs do sistema.
```
#!/bin/bash
# Script para imprimir logs do sistema

echo "Exibindo logs do sistema:"

# Exibindo as últimas 10 mensagens do log do sistema
dmesg | tail -n 10

echo "Fim dos logs do sistema."

```
#### 6º Passo: Torne o script executável através do comadno `chmod +x log_script.sh`.
#### 7° Passo: Execute o script `./log_script.sh`
#### 8° Passo: Saia do container com o comando `exit`

## 3.Listando e removendo containers liste todos os containers em execução e parados, pare um container em execução e remova um container específico.
#### 1º Passo: Listar todos os containers incluindo os que estão parados -> `docker ps -a`
#### 2° Passo: Parar um container -> `docker stop <nome ou id do container>`
#### 3° Paso: Remover um container (só épossível remover containers parados) -> `docker rm <nome ou id do container`  
Obs.: No ID do container pode ser utilizados apenas os 3 primeiros digitos.  
## 4.Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.
#### 1º Passo: Crie um diretório para a aplicação e acesse-o:
```
mkdir flask-app
cd flask-app
```
#### 2º Passo: Crie o arquivo app.py e coloque o conteúdo abaixo:
`nano app.py`  
Use o seguinte código dentro do arquivo:
  
```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello world!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)

```
#### 3º Passo: Crie o arquivo de dependências(requirements.txt)
`nano requirements.txt`  
Adicione as seguintes dependências:  
```
Flask==2.0.1
Werkzeug==2.0.3
```

#### 4º Passo: Crie o Dockerfile
`nano Dockerfile`  
Coloque o seguinte conteúdo: 
```
# Usando uma imagem base do Python
FROM python:3.9-slim

# Definir diretório de trabalho dentro do container
WORKDIR /app

# Copiar os arquivos para o container
COPY . .

# Instalar as dependências
RUN pip install -r requirements.txt

# Expor a porta 5000
EXPOSE 5000

# Comando para rodar a aplicação
CMD ["python", "app.py"]
```
#### 5º Passo: Construa a imagem do container
`docker build -t flask-app .`
#### 6º Passo: Executar o container:
`docker run -p 5000:5000 flask-app`
#### 7º Passo: Abra o navegador e acesse
`http://localhost:5000`
## 5.Criando e utilizando volumes para persistência de dados.
#### 1º Passo: Criar e acessar o diretório do projeto.
`mkdir laravel-breeze-mysql`  
`cd laravel-breeze-mysql`  
#### 2º Passo: crie o  arquivo docker-compose-yml.  
`nano docker-compose-yml`  
Com o seguinte conteúdo:  
```
services:
  mysql:
    image: mysql:8.0
    container_name: mysql-container
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: laravel_db
      MYSQL_USER: laravel_user
      MYSQL_PASSWORD: laravel_password
    volumes:
      - mysql-data:/var/lib/mysql
    ports:
      - "3306:3306"
    networks:
      - laravel_network

  laravel:
    image: laravelphp/php-fpm
    container_name: laravel-container
    build:
      context: .
      dockerfile: Dockerfile
    volumes:
      - .:/var/www/html
    ports:
      - "9000:9000"
    depends_on:
      - mysql
    networks:
      - laravel_network

volumes:
  mysql-data:
    driver: local

networks:
  laravel_network:
    driver: bridge

```
#### 3º Passo: criar o Dockerfile  
`nano Dockerfile`  
Com o seguinte conteúdo:
```
FROM php:8.0-fpm

# Instalar dependências do PHP e extensões necessárias
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    zip \
    git \
    libicu-dev \
    curl \
    gnupg2 \
    lsb-release \
    ca-certificates \
    apt-transport-https

# Instalar o Node.js e o npm
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash - && \
    apt-get install -y nodejs

# Instalar o Composer (gerenciador de dependências do Laravel)
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Definir diretório de trabalho
WORKDIR /var/www/html

```
#### 4º Passo: Criar e subir o container.  
- Construir imagem: `docker-compose build`  
- Subir os containers: `docker-compose up -d`  

#### 5º Passo: Instalar o Laravel.
Entre no container Laravel: `docker-compose exec laravel bash`
Instale-o usando o composer: `composer create-project --prefer-dist laravel/laravel .`  
#### 6º Passo: Instalar Laravel Breeze (Autenticação).  
- Instale o pacote Laravel Breeze:`composer require laravel/breeze --dev`  
- Escolher a stack para o Laravel Breeze: `php artisan breeze:install`  
- Instalar dependências do npm: `npm install`  
- Compile os assets com o npm: `npm run dev`
#### 7º Passo: Configurar o Banco de Dados MySQL.
- Abra o arquivo .env do Laravel e faça as seguuintes configurações:
```
DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=laravel_password

```
- Execute as migrações: `php artisan migrate`
#### 7º Passo: Testar a aplicação.
- abra o navegador e digite: `http://localhost:9000`
## 6. Criando e rodando um container multi-stage utilizando um multi-stage build para otimizar uma aplicação Go, reduzindo o tamanho da imagem final.
#### 1º Passo:Criar uma pasta para o projeto e acessa-la:
`mkdir go-fiber-app`  
`cd go-fiber-app`
#### 2º Passo: Criar um arquivo main.go.
`nano main.go`  
Coloque o seguinte conteúdo:
```
package main

import (
	"log"
	"github.com/gofiber/fiber/v2"
)

func main() {
	app := fiber.New()

	app.Get("/", func(c *fiber.Ctx) error {
		return c.SendString("Hello, World!")
	})

	log.Fatal(app.Listen(":3000"))
}

```
#### 3º Passo: Instalar as dependências Go e Go Fiber.
`go mod init go-fiber-app`  
`go get github.com/gofiber/fiber/v2`
#### 4º Passo: Criar um Dockerfile.
`sudo nano Dockerfile`  
Coloque o seguinte conteúdo:  
```
# Etapa 1: Construção
FROM golang:1.20-alpine AS builder

# Instalar dependências
RUN apk add --no-cache git

# Definir o diretório de trabalho no container
WORKDIR /app

# Copiar o código da aplicação para o container
COPY . .

# Baixar dependências e compilar o código
RUN go mod tidy
RUN go build -o app .

# Etapa 2: Imagem final
FROM alpine:latest

# Instalar dependências necessárias para rodar a aplicação
RUN apk --no-cache add ca-certificates

# Copiar o arquivo compilado da fase de build
COPY --from=builder /app/app /app

# Definir a variável de ambiente para a porta
ENV PORT=3000

# Expor a porta que o app vai rodar
EXPOSE 3000

# Comando para rodar a aplicação
CMD ["/app"]
```
#### 5º Passo: construir a imagem docker.
`docker build -t go-fiber-app .`  
#### 6º Passo: Rodar o container.
`docker run -p 3000:3000 go-fiber-app`
#### 7º Passo: Testar a aplicação.
Acesse o navegador e digite:  
`http://localhost:3000`  
você verá a mensagem: Hello World!  
## 7.Construindo uma Rede Docker para Comunicação entre Containers (MEAN Todos)

#### Estrutura do Projeto

```
mean-todos/
│
├── backend/
│   ├── server.js
│   ├── package.json
│   ├── Dockerfile
│   └── .dockerignore
│
├── docker-compose.yml
└── .env
```

---

## 1º Passo: Adicione o conteúdo do `server.js`

```
const express = require('express');
const mongoose = require('mongoose');

const app = express();
const port = 3000;

mongoose.connect('mongodb://mongo:27017/todos', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

const TodoSchema = new mongoose.Schema({
  text: String,
});

const Todo = mongoose.model('Todo', TodoSchema);

app.use(express.json());

app.post('/todos', async (req, res) => {
  const todo = new Todo(req.body);
  await todo.save();
  res.send(todo);
});

app.get('/todos', async (req, res) => {
  const todos = await Todo.find();
  res.send(todos);
});

app.listen(port, () => {
  console.log(`Servidor rodando na porta ${port}`);
});
```

---

#### 2º Passo: adicione o conteúdo do `package.json`

```
{
  "name": "mean-todos-backend",
  "version": "1.0.0",
  "description": "Backend do projeto MEAN Todos com Node.js e MongoDB",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.17.1",
    "mongoose": "^6.0.0"
  }
}
```

---

#### 3º Passo: Adicione o conteúdo do `Dockerfile`

```
# Use uma imagem base do Node.js
FROM node:16-alpine

# Defina o diretório de trabalho
WORKDIR /app

# Copie o arquivo package.json e package-lock.json (se existir)
COPY package*.json ./

# Instale as dependências
RUN npm install

# Copie o restante do código
COPY . .

# Exponha a porta
EXPOSE 3000

# Comando para rodar o servidor
CMD ["node", "server.js"]
```

---

#### 4º Passo: Adicione o conteúdo do `docker-compose.yml`

```
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - "3000:3000"
    depends_on:
      - mongo
    networks:
      - mean_network

  mongo:
    image: mongo:latest
    ports:
      - "27017:27017"
    networks:
      - mean_network
    volumes:
      - mongo_data:/data/db

networks:
  mean_network:

volumes:
  mongo_data:
```

---

#### 5º Passo: Adicione o conteúdo do `.env`.

```
MONGO_URI=mongodb://mongo:27017/todos
```

---

#### 6º Passo: Criar o arquivo `.dockerignore`

No diretório `backend/`, crie o arquivo `.dockerignore` com o seguinte conteúdo:

```
node_modules
npm-debug.log
```

---

#### 7º Passo: Construir e rodar os containers

No terminal, na raiz do projeto (`mean-todos`), execute:

```
docker-compose up --build
```

---

#### 8º Passo: Testar a API

Para adicionar uma tarefa:

```
curl -X POST http://localhost:3000/todos \
-H "Content-Type: application/json" \
-d '{"text": "Aprender Docker"}'
```

Para listar as tarefas:

```
curl http://localhost:3000/todos
```

---

#### 9º Passo: Parar os containers

```
docker-compose down
```
## 8.Criando um compose file para rodar uma aplicação com banco de dados. Utilize Docker Compose para configurar uma aplicação Django com um banco de dados PostgreSQL.
#### 1º Passo: Crie os seguintes arquivos e pastas no diretório do projeto.
- `nano Dockerfile`: Arquivo para configurar a construção da imagem do container.
```
  # Usar uma imagem base do Python
FROM python:3.9

# Definir o diretório de trabalho
WORKDIR /app

# Copiar o arquivo de dependências
COPY requirements.txt /app/

# Instalar dependências
RUN pip install --no-cache-dir -r requirements.txt

# Copiar todo o código da aplicação
COPY . /app/

# Expôr a porta para a aplicação
EXPOSE 8000

# Rodar o servidor do Django
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

```
- `nano docker-compose.yml`: Arquivo para orquestrar os containers.
```
services:
  web:
    build: .
    command: bash -c "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    depends_on:
      - db
    environment:
      - DEBUG=True

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: polls_db
      POSTGRES_USER: polls_user
      POSTGRES_PASSWORD: polls_pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```
- `nano requirements.txt`: Arquivo para instalar as dependências para o postgree.
```
Django>=3.2,<4.0
psycopg2-binary

``` 
- `mkdir myapp/`: Pasta com o código da aplicação Django.
- `nano .dockerignore`: Arquivo para garantir que arquivos desnecessários não sejam copiados para a imagem Docker.
```
__pycache__
.dockerignore
Dockerfile
docker-compose.yml
*.pyc
*.pyo
```
#### 2º Passo: No diretório raiz execute o seguinte comando para iniciar um projeto Django.
`docker-compose run web django-admin startproject myapp .`
#### 3º Passo: onfigure a conexão com o banco de dados PostgreSQL.
- `nano myapp/settings.py`
- Deixe a parte de databases assim:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'polls_db',
        'USER': 'polls_user',
        'PASSWORD': 'polls_pass',
        'HOST': 'db',  # Nome do serviço do banco no docker-compose.yml
        'PORT': '5432',
    }
}

```
#### 4º Passo: Contruir e rodar os containers.  
`docker-compose up --build` : para construir as imagens e rodar os conatiners.  
Obs.: Espere um tempo antes de próximo passo pois demora um tempo até o Django se conectar com o Postgree.
#### 5º Passo: Execute as migrações para preparar o banco de dados.  
`docker-compose exec web python manage.py migrate`
#### 6º Passo: Acessar a Aplicação Django.
Acesse o navegador e coloque `http://localhost:8000`na URL e aparecerá a pagina inicial do Django.
## 9.Criando uma imagem personalizada com um servidor web e arquivos estáticos. Construa uma imagem baseada no Nginx ou Apache, adicionando um site HTML/CSS estático.
#### 1º Passo: Crie um diretório para o projeto com as seguintes arquivos.
-`Index.html`
```
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Minha Landing Page</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <h1>Bem-vindo à Minha Página</h1>
        <p>Essa é uma página de exemplo hospedada no Docker usando o Nginx.</p>
    </header>
    <footer>
        <p>&copy; 2025 Minha Página</p>
    </footer>
</body>
</html>

```
-`style.css`
```
body {
    font-family: Arial, sans-serif;
    background-color: #f4f4f4;
    color: #333;
    margin: 0;
    padding: 0;
}

header {
    background-color: #333;
    color: white;
    padding: 20px;
    text-align: center;
}

footer {
    background-color: #333;
    color: white;
    text-align: center;
    padding: 10px;
    position: fixed;
    width: 100%;
    bottom: 0;
}

```
-`Dockerfile`
```
# Use a imagem oficial do Nginx como base
FROM nginx:latest

# Copie os arquivos HTML e CSS para o diretório de conteúdo do Nginx
COPY index.html /usr/share/nginx/html/index.html
COPY style.css /usr/share/nginx/html/style.css

# Exponha a porta 80 para acessar o site
EXPOSE 80

# Inicie o Nginx no modo foreground
CMD ["nginx", "-g", "daemon off;"]

```
#### 2º Passo: Construir a imagem Docker.  
`docker build -t minha-landing-page .`: Este comando vai criar uma imagem chamada minha-landing-page.
#### 3º Passo: Rodar o Container: 
`docker run -d -p 8080:80 --name meu-container minha-landing-page`
#### 4º Passo: Acessar o conatiner:  
Abra o navegador e acesse `http://localhost:8080` para ver sua landing page sendo servida pelo Nginx no Docker.


