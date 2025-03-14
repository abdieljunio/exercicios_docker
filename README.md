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
