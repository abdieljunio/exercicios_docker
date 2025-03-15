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
## 3.Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.
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
