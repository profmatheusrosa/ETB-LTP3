# EXPLICAÇÃO DETALHADA DOS CÓDIGOS DE SERVLETS
## Análise Completa - Linha por Linha

---

## 📋 ÍNDICE

1. [Estrutura Básica de um Servlet](#estrutura-básica)
2. [Ciclo de Vida - Métodos Fundamentais](#ciclo-de-vida)
3. [Exercício 1: Primeiro Servlet](#exercicio-1)
4. [Exercício 2: Formulários GET/POST](#exercicio-2)
5. [Exercício 3: Sessões e Login](#exercicio-3)
6. [Configurações e Dicas](#configuracoes)

---

## 🔧 ESTRUTURA BÁSICA DE UM SERVLET

### **Anatomia de um Servlet:**

```java
package com.exemplo;  // 1. Declaração do pacote

// 2. Imports necessários
import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

// 3. Anotação para mapeamento
@WebServlet(name = "NomeServlet", urlPatterns = {"/url-acesso"})

// 4. Classe que estende HttpServlet
public class NomeServlet extends HttpServlet {
    
    // 5. Método para requisições GET
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // Código aqui
    }
    
    // 6. Método para requisições POST
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // Código aqui
    }
}
```

### **Explicação Detalhada:**

#### **1. Package (Pacote):**
```java
package com.exemplo;
```
- **Função:** Organiza as classes em grupos lógicos
- **Convenção:** `com.empresa.projeto` ou `com.exemplo`
- **Importância:** Evita conflitos de nomes entre classes

#### **2. Imports (Importações):**
```java
import java.io.IOException;           // Para tratamento de erros de I/O
import java.io.PrintWriter;          // Para escrever resposta HTML
import javax.servlet.ServletException; // Exceção específica de Servlets
import javax.servlet.annotation.WebServlet; // Anotação para mapeamento
import javax.servlet.http.HttpServlet;     // Classe base para Servlets HTTP
import javax.servlet.http.HttpServletRequest;  // Objeto da requisição
import javax.servlet.http.HttpServletResponse; // Objeto da resposta
```

#### **3. Anotação @WebServlet:**
```java
@WebServlet(name = "NomeServlet", urlPatterns = {"/url-acesso"})
```
- **`name`:** Nome interno do Servlet (opcional)
- **`urlPatterns`:** URLs que ativam este Servlet
- **Exemplos:**
  - `{"/ola"}` → Acessa via `http://localhost:8080/projeto/ola`
  - `{"/usuario/*"}` → Qualquer URL que comece com `/usuario/`
  - `{"/", "/home"}` → Múltiplas URLs

#### **4. Herança de HttpServlet:**
```java
public class NomeServlet extends HttpServlet
```
- **Por que estender HttpServlet?**
  - Fornece métodos prontos (`doGet`, `doPost`)
  - Implementa o ciclo de vida padrão
  - Facilita o tratamento de requisições HTTP

---

## 🔄 CICLO DE VIDA - MÉTODOS FUNDAMENTAIS

### **1. Método init() - Inicialização:**

```java
public void init() throws ServletException {
    // Executado UMA VEZ quando o Servlet é carregado
    // Aqui você faz configurações iniciais
}
```

**Explicação Detalhada:**
- **Quando executa:** Apenas uma vez, quando o servidor carrega o Servlet
- **O que fazer aqui:**
  - Carregar arquivos de configuração
  - Estabelecer conexões com banco de dados
  - Inicializar variáveis globais
- **Exemplo prático:**
```java
public void init() throws ServletException {
    // Carregar configurações do arquivo
    Properties config = new Properties();
    try {
        config.load(getServletContext().getResourceAsStream("/WEB-INF/config.properties"));
        this.databaseUrl = config.getProperty("database.url");
    } catch (IOException e) {
        throw new ServletException("Erro ao carregar configurações", e);
    }
}
```

### **2. Método service() - Processamento:**

```java
public void service(HttpServletRequest request, HttpServletResponse response) {
    // Executado para CADA requisição
    // Aqui você processa as requisições
}
```

**Explicação Detalhada:**
- **Quando executa:** Para cada requisição HTTP recebida
- **Parâmetros:**
  - `request`: Contém dados da requisição (parâmetros, cabeçalhos, etc.)
  - `response`: Para enviar a resposta ao cliente
- **Fluxo interno:** O `service()` chama `doGet()` ou `doPost()` automaticamente

### **3. Método destroy() - Limpeza:**

```java
public void destroy() {
    // Executado quando o Servlet é descarregado
    // Aqui você faz limpeza de recursos
}
```

**Explicação Detalhada:**
- **Quando executa:** Quando o servidor descarrega o Servlet
- **O que fazer aqui:**
  - Fechar conexões com banco de dados
  - Salvar dados em arquivos
  - Liberar recursos alocados
- **Exemplo prático:**
```java
public void destroy() {
    // Fechar conexão com banco
    if (connection != null) {
        try {
            connection.close();
        } catch (SQLException e) {
            // Log do erro
        }
    }
}
```

---

## 🎯 EXERCÍCIO 1: PRIMEIRO SERVLET

### **Código Completo:**

```java
package com.exemplo;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "OlaServlet", urlPatterns = {"/ola"})
public class OlaServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        response.setContentType("text/html;charset=UTF-8");
        
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Meu Primeiro Servlet</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h1>Olá Mundo! Este é meu primeiro Servlet!</h1>");
            out.println("<p>Data e hora: " + new java.util.Date() + "</p>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

### **Análise Linha por Linha:**

#### **Linha 1-8: Imports e Estrutura**
```java
package com.exemplo;
```
- Define o pacote onde a classe está localizada

```java
import java.io.IOException;
import java.io.PrintWriter;
```
- `IOException`: Para tratamento de erros de entrada/saída
- `PrintWriter`: Classe para escrever texto na resposta HTTP

```java
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
```
- `ServletException`: Exceção específica para problemas em Servlets
- `@WebServlet`: Anotação para mapear URL
- `HttpServlet`: Classe base para Servlets HTTP
- `HttpServletRequest`: Contém dados da requisição
- `HttpServletResponse`: Para enviar resposta

#### **Linha 10: Anotação de Mapeamento**
```java
@WebServlet(name = "OlaServlet", urlPatterns = {"/ola"})
```
- **`name`:** Identificador interno do Servlet
- **`urlPatterns`:** Define que este Servlet responde à URL `/ola`
- **Resultado:** `http://localhost:8080/projeto/ola` ativa este Servlet

#### **Linha 11: Declaração da Classe**
```java
public class OlaServlet extends HttpServlet {
```
- `public`: Classe acessível de qualquer lugar
- `extends HttpServlet`: Herda funcionalidades básicas de Servlet HTTP

#### **Linha 13-15: Método doGet**
```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
```
- `@Override`: Indica que está sobrescrevendo método da classe pai
- `protected`: Método acessível apenas dentro do pacote e subclasses
- `doGet`: Executado quando recebe requisição GET (links, digitação de URL)
- `throws`: Declara que pode lançar essas exceções

#### **Linha 17: Configuração do Tipo de Conteúdo**
```java
response.setContentType("text/html;charset=UTF-8");
```
- **Função:** Informa ao navegador que a resposta é HTML
- **`text/html`:** Tipo MIME para páginas HTML
- **`charset=UTF-8`:** Codificação de caracteres (suporta acentos)

#### **Linha 19: Try-with-Resources**
```java
try (PrintWriter out = response.getWriter()) {
```
- **`response.getWriter()`:** Obtém objeto para escrever na resposta
- **`try-with-resources`:** Fecha automaticamente o `PrintWriter` ao final
- **Vantagem:** Não precisa fechar manualmente

#### **Linha 20-27: Geração do HTML**
```java
out.println("<!DOCTYPE html>");
out.println("<html>");
out.println("<head>");
out.println("<title>Meu Primeiro Servlet</title>");
out.println("</head>");
out.println("<body>");
out.println("<h1>Olá Mundo! Este é meu primeiro Servlet!</h1>");
out.println("<p>Data e hora: " + new java.util.Date() + "</p>");
out.println("</body>");
out.println("</html>");
```
- **`out.println()`:** Escreve texto na resposta HTTP
- **`new java.util.Date()`:** Cria objeto com data/hora atual
- **Resultado:** Gera página HTML completa

---

## 📝 EXERCÍCIO 2: FORMULÁRIO COM GET E POST

### **1. Página HTML - Análise Detalhada:**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Formulário de Cadastro</title>
    <meta charset="UTF-8">
</head>
<body>
    <h2>Cadastro de Usuário</h2>
    
    <form action="CadastroServlet" method="post">
        <p>
            <label>Nome:</label>
            <input type="text" name="nome" required>
        </p>
        <p>
            <label>Email:</label>
            <input type="email" name="email" required>
        </p>
        <p>
            <label>Idade:</label>
            <input type="number" name="idade" min="1" max="120">
        </p>
        <p>
            <label>Cidade:</label>
            <select name="cidade">
                <option value="SP">São Paulo</option>
                <option value="RJ">Rio de Janeiro</option>
                <option value="MG">Minas Gerais</option>
            </select>
        </p>
        <p>
            <input type="submit" value="Cadastrar">
        </p>
    </form>
    
    <hr>
    
    <h3>Buscar Usuário (GET)</h3>
    <form action="BuscaServlet" method="get">
        <p>
            <label>Buscar por nome:</label>
            <input type="text" name="busca">
            <input type="submit" value="Buscar">
        </p>
    </form>
</body>
</html>
```

### **Análise dos Elementos HTML:**

#### **Formulário POST (Cadastro):**
```html
<form action="CadastroServlet" method="post">
```
- **`action="CadastroServlet"`:** URL que receberá os dados
- **`method="post"`:** Método HTTP para envio de dados

#### **Campos do Formulário:**
```html
<input type="text" name="nome" required>
```
- **`type="text"`:** Campo de texto simples
- **`name="nome"`:** Nome do parâmetro (usado no Servlet)
- **`required`:** Validação HTML5 (campo obrigatório)

```html
<input type="email" name="email" required>
```
- **`type="email"`:** Validação automática de formato de email

```html
<input type="number" name="idade" min="1" max="120">
```
- **`type="number"`:** Aceita apenas números
- **`min="1" max="120"`:** Limites de valores

```html
<select name="cidade">
    <option value="SP">São Paulo</option>
    <option value="RJ">Rio de Janeiro</option>
    <option value="MG">Minas Gerais</option>
</select>
```
- **`select`:** Lista dropdown
- **`value="SP"`:** Valor enviado ao servidor
- **`São Paulo`:** Texto exibido ao usuário

#### **Formulário GET (Busca):**
```html
<form action="BuscaServlet" method="get">
```
- **`method="get"`:** Dados aparecem na URL
- **Exemplo:** `BuscaServlet?busca=João`

### **2. Servlet para Cadastro (POST) - Análise Detalhada:**

```java
package com.exemplo;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "CadastroServlet", urlPatterns = {"/CadastroServlet"})
public class CadastroServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        // Obter parâmetros
        String nome = request.getParameter("nome");
        String email = request.getParameter("email");
        String idade = request.getParameter("idade");
        String cidade = request.getParameter("cidade");
        
        response.setContentType("text/html;charset=UTF-8");
        
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Usuário Cadastrado</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Usuário Cadastrado com Sucesso!</h2>");
            out.println("<p><strong>Nome:</strong> " + nome + "</p>");
            out.println("<p><strong>Email:</strong> " + email + "</p>");
            out.println("<p><strong>Idade:</strong> " + idade + "</p>");
            out.println("<p><strong>Cidade:</strong> " + cidade + "</p>");
            out.println("<a href='index.html'>Voltar</a>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

### **Análise Linha por Linha:**

#### **Linha 15: Método doPost**
```java
protected void doPost(HttpServletRequest request, HttpServletResponse response)
```
- **`doPost`:** Executado quando recebe requisição POST
- **Diferença do GET:** Dados não aparecem na URL

#### **Linha 18-21: Obtenção de Parâmetros**
```java
String nome = request.getParameter("nome");
String email = request.getParameter("email");
String idade = request.getParameter("idade");
String cidade = request.getParameter("cidade");
```
- **`request.getParameter("nome")`:** Obtém valor do campo com `name="nome"`
- **Retorna:** `String` com o valor ou `null` se não existir
- **Importante:** Sempre retorna `String`, mesmo para números

#### **Linha 23: Configuração da Resposta**
```java
response.setContentType("text/html;charset=UTF-8");
```
- Define que a resposta será HTML com suporte a acentos

#### **Linha 25-37: Geração da Página de Confirmação**
```java
out.println("<h2>Usuário Cadastrado com Sucesso!</h2>");
out.println("<p><strong>Nome:</strong> " + nome + "</p>");
```
- **`" + nome + "`:** Concatenação de strings em Java
- **`<strong>`:** Tag HTML para texto em negrito
- **Resultado:** Exibe os dados recebidos do formulário

### **3. Servlet para Busca (GET) - Análise Detalhada:**

```java
package com.exemplo;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "BuscaServlet", urlPatterns = {"/BuscaServlet"})
public class BuscaServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        String busca = request.getParameter("busca");
        
        response.setContentType("text/html;charset=UTF-8");
        
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Resultado da Busca</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Resultado da Busca</h2>");
            
            if (busca != null && !busca.trim().isEmpty()) {
                out.println("<p>Buscando por: <strong>" + busca + "</strong></p>");
                out.println("<p>Resultados encontrados: 0 (simulação)</p>");
            } else {
                out.println("<p>Nenhum termo de busca fornecido.</p>");
            }
            
            out.println("<a href='index.html'>Voltar</a>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

### **Análise da Lógica de Validação:**

#### **Linha 16: Obtenção do Parâmetro**
```java
String busca = request.getParameter("busca");
```
- Obtém o termo de busca da URL

#### **Linha 25-29: Validação e Lógica Condicional**
```java
if (busca != null && !busca.trim().isEmpty()) {
    out.println("<p>Buscando por: <strong>" + busca + "</strong></p>");
    out.println("<p>Resultados encontrados: 0 (simulação)</p>");
} else {
    out.println("<p>Nenhum termo de busca fornecido.</p>");
}
```
- **`busca != null`:** Verifica se o parâmetro existe
- **`!busca.trim().isEmpty()`:** Verifica se não está vazio (após remover espaços)
- **`trim()`:** Remove espaços no início e fim
- **`isEmpty()`:** Verifica se a string está vazia

---

## 🔐 EXERCÍCIO 3: SESSÕES E LOGIN

### **1. Página de Login - Análise:**

```html
<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
    <meta charset="UTF-8">
</head>
<body>
    <h2>Login do Sistema</h2>
    
    <form action="LoginServlet" method="post">
        <p>
            <label>Usuário:</label>
            <input type="text" name="usuario" required>
        </p>
        <p>
            <label>Senha:</label>
            <input type="password" name="senha" required>
        </p>
        <p>
            <input type="submit" value="Entrar">
        </p>
    </form>
</body>
</html>
```

### **Análise dos Campos:**
- **`type="password"`:** Oculta os caracteres digitados
- **`required`:** Ambos os campos são obrigatórios

### **2. Servlet de Login - Análise Detalhada:**

```java
package com.exemplo;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@WebServlet(name = "LoginServlet", urlPatterns = {"/LoginServlet"})
public class LoginServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        String usuario = request.getParameter("usuario");
        String senha = request.getParameter("senha");
        
        // Simulação de autenticação
        if ("admin".equals(usuario) && "123456".equals(senha)) {
            // Login válido - criar sessão
            HttpSession session = request.getSession();
            session.setAttribute("usuario", usuario);
            session.setAttribute("logado", true);
            
            // Redirecionar para área restrita
            response.sendRedirect("AreaRestritaServlet");
        } else {
            // Login inválido - redirecionar de volta
            response.sendRedirect("login.html?erro=1");
        }
    }
}
```

### **Análise Linha por Linha:**

#### **Linha 18-19: Obtenção de Credenciais**
```java
String usuario = request.getParameter("usuario");
String senha = request.getParameter("senha");
```
- Obtém usuário e senha do formulário

#### **Linha 22: Validação de Credenciais**
```java
if ("admin".equals(usuario) && "123456".equals(senha)) {
```
- **`"admin".equals(usuario)`:** Comparação segura de strings
- **Por que usar `.equals()`?**
  - `==` compara referências de objetos
  - `.equals()` compara conteúdo das strings
- **Exemplo:**
  ```java
  String a = "admin";
  String b = "admin";
  System.out.println(a == b);        // false (referências diferentes)
  System.out.println(a.equals(b));   // true (conteúdo igual)
  ```

#### **Linha 24-27: Criação da Sessão**
```java
HttpSession session = request.getSession();
session.setAttribute("usuario", usuario);
session.setAttribute("logado", true);
```
- **`request.getSession()`:** Obtém sessão existente ou cria nova
- **`setAttribute("chave", valor)`:** Armazena dados na sessão
- **Dados armazenados:**
  - `"usuario"` → nome do usuário logado
  - `"logado"` → flag indicando que está autenticado

#### **Linha 30: Redirecionamento de Sucesso**
```java
response.sendRedirect("AreaRestritaServlet");
```
- **`sendRedirect()`:** Envia resposta HTTP 302 (redirecionamento)
- **Navegador:** Faz nova requisição para a URL especificada
- **URL no navegador:** Muda para `AreaRestritaServlet`

#### **Linha 33: Redirecionamento de Erro**
```java
response.sendRedirect("login.html?erro=1");
```
- **`?erro=1`:** Parâmetro na URL para indicar erro
- **Pode ser usado para:** Mostrar mensagem de erro na página de login

### **3. Servlet da Área Restrita - Análise Detalhada:**

```java
package com.exemplo;

import java.io.IOException;
import java.io.PrintWriter;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@WebServlet(name = "AreaRestritaServlet", urlPatterns = {"/AreaRestritaServlet"})
public class AreaRestritaServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        HttpSession session = request.getSession(false);
        
        // Verificar se está logado
        if (session == null || session.getAttribute("logado") == null) {
            response.sendRedirect("login.html");
            return;
        }
        
        String usuario = (String) session.getAttribute("usuario");
        
        response.setContentType("text/html;charset=UTF-8");
        
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Área Restrita</title>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Bem-vindo à Área Restrita!</h2>");
            out.println("<p>Usuário logado: <strong>" + usuario + "</strong></p>");
            out.println("<p>Esta é uma página protegida por sessão.</p>");
            out.println("<a href='LogoutServlet'>Sair</a>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

### **Análise da Proteção por Sessão:**

#### **Linha 18: Obtenção da Sessão**
```java
HttpSession session = request.getSession(false);
```
- **`getSession(false)`:** Obtém sessão existente, mas NÃO cria nova
- **Diferença de `getSession()`:** Sem parâmetro cria sessão se não existir

#### **Linha 21-24: Verificação de Autenticação**
```java
if (session == null || session.getAttribute("logado") == null) {
    response.sendRedirect("login.html");
    return;
}
```
- **`session == null`:** Verifica se não há sessão
- **`session.getAttribute("logado") == null`:** Verifica se não está logado
- **`return`:** Para a execução do método (não executa o resto)

#### **Linha 26: Recuperação de Dados da Sessão**
```java
String usuario = (String) session.getAttribute("usuario");
```
- **`(String)`:** Cast explícito (conversão de tipo)
- **Necessário porque:** `getAttribute()` retorna `Object`

### **4. Servlet de Logout - Análise Detalhada:**

```java
package com.exemplo;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

@WebServlet(name = "LogoutServlet", urlPatterns = {"/LogoutServlet"})
public class LogoutServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        HttpSession session = request.getSession(false);
        if (session != null) {
            session.invalidate();
        }
        
        response.sendRedirect("login.html");
    }
}
```

### **Análise do Processo de Logout:**

#### **Linha 18: Obtenção da Sessão**
```java
HttpSession session = request.getSession(false);
```
- Obtém sessão existente sem criar nova

#### **Linha 19-21: Invalidação da Sessão**
```java
if (session != null) {
    session.invalidate();
}
```
- **`session.invalidate()`:** Destrói completamente a sessão
- **Resultado:** Todos os dados são perdidos
- **Usuário:** Precisa fazer login novamente

#### **Linha 24: Redirecionamento**
```java
response.sendRedirect("login.html");
```
- Volta para a página de login

---

## 🔧 CONFIGURAÇÕES E DICAS

### **1. Configuração do web.xml (Alternativa à Anotação):**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.1" xmlns="http://xmlns.jcp.org/xml/ns/javaee" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd">
    
    <servlet>
        <servlet-name>MeuServlet</servlet-name>
        <servlet-class>com.exemplo.MeuServlet</servlet-class>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>MeuServlet</servlet-name>
        <url-pattern>/meu-servlet</url-pattern>
    </servlet-mapping>
    
</web-app>
```

### **Análise do web.xml:**

#### **Elemento servlet:**
```xml
<servlet>
    <servlet-name>MeuServlet</servlet-name>
    <servlet-class>com.exemplo.MeuServlet</servlet-class>
</servlet>
```
- **`servlet-name`:** Nome interno do Servlet
- **`servlet-class`:** Caminho completo da classe Java

#### **Elemento servlet-mapping:**
```xml
<servlet-mapping>
    <servlet-name>MeuServlet</servlet-name>
    <url-pattern>/meu-servlet</url-pattern>
</servlet-mapping>
```
- **`servlet-name`:** Referência ao nome definido acima
- **`url-pattern`:** URL que ativa o Servlet

### **2. Tratamento de Erros:**

```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    
    try {
        // Seu código aqui
        String parametro = request.getParameter("dado");
        if (parametro == null) {
            throw new IllegalArgumentException("Parâmetro obrigatório não fornecido");
        }
        
        // Processamento normal...
        
    } catch (Exception e) {
        response.sendError(HttpServletResponse.SC_INTERNAL_SERVER_ERROR, 
                          "Erro interno do servidor");
    }
}
```

### **Análise do Tratamento de Erros:**

#### **Try-Catch:**
```java
try {
    // Código que pode gerar erro
} catch (Exception e) {
    // Tratamento do erro
}
```

#### **Códigos de Status HTTP:**
```java
response.sendError(HttpServletResponse.SC_INTERNAL_SERVER_ERROR, "Mensagem");
```
- **`SC_INTERNAL_SERVER_ERROR`:** Erro 500
- **`SC_BAD_REQUEST`:** Erro 400
- **`SC_NOT_FOUND`:** Erro 404
- **`SC_UNAUTHORIZED`:** Erro 401

### **3. Configuração de Sessão:**

```java
// Configurar tempo de sessão (em segundos)
HttpSession session = request.getSession();
session.setMaxInactiveInterval(1800); // 30 minutos
```

### **Análise da Configuração de Sessão:**

#### **setMaxInactiveInterval:**
```java
session.setMaxInactiveInterval(1800);
```
- **Parâmetro:** Tempo em segundos
- **1800 segundos = 30 minutos**
- **Comportamento:** Sessão expira após 30 minutos de inatividade

#### **Outras Configurações Úteis:**
```java
// Verificar se é uma sessão nova
boolean isNew = session.isNew();

// Obter ID da sessão
String sessionId = session.getId();

// Verificar tempo de criação
long creationTime = session.getCreationTime();

// Verificar último acesso
long lastAccessTime = session.getLastAccessedTime();
```

---

## 📚 RESUMO DOS CONCEITOS IMPORTANTES

### **1. Diferenças entre GET e POST:**

| **Aspecto** | **GET** | **POST** |
|-------------|---------|----------|
| **Visibilidade** | Dados na URL | Dados ocultos |
| **Tamanho** | Limitado (2048 chars) | Ilimitado |
| **Cache** | Pode ser cacheado | Não é cacheado |
| **Segurança** | Menos seguro | Mais seguro |
| **Uso** | Buscas, navegação | Formulários, uploads |

### **2. Ciclo de Vida do Servlet:**

```
1. Servidor Inicia → Servlet é carregado
2. init() é chamado (UMA VEZ)
3. Cliente faz requisição
4. service() → doGet() ou doPost()
5. Servlet processa e responde
6. Servidor descarrega Servlet
7. destroy() é chamado
```

### **3. Gerenciamento de Sessão:**

```java
// Criar/obter sessão
HttpSession session = request.getSession();

// Armazenar dados
session.setAttribute("chave", valor);

// Recuperar dados
Object valor = session.getAttribute("chave");

// Remover dados
session.removeAttribute("chave");

// Destruir sessão
session.invalidate();
```

### **4. Redirecionamento vs Encaminhamento:**

| **Aspecto** | **sendRedirect()** | **forward()** |
|-------------|-------------------|---------------|
| **Requisição** | Nova requisição HTTP | Mesma requisição |
| **URL** | Muda no navegador | Não muda |
| **Dados** | Perde dados originais | Mantém dados |
| **Uso** | Mudança de contexto | Processamento interno |

---

## 🎯 CONCEITOS AVANÇADOS

### **1. Filtros (Filters):**
```java
@WebFilter("/admin/*")
public class AuthFilter implements Filter {
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
        // Verificar autenticação antes de qualquer Servlet
        chain.doFilter(request, response);
    }
}
```

### **2. Listeners:**
```java
@WebListener
public class SessionListener implements HttpSessionListener {
    public void sessionCreated(HttpSessionEvent se) {
        // Sessão foi criada
    }
    
    public void sessionDestroyed(HttpSessionEvent se) {
        // Sessão foi destruída
    }
}
```

### **3. Upload de Arquivos:**
```java
@MultipartConfig
public class UploadServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) {
        Part filePart = request.getPart("arquivo");
        String fileName = filePart.getSubmittedFileName();
        // Processar arquivo...
    }
}
```

---

*Esta explicação detalhada cobre todos os aspectos dos códigos de Servlets, desde conceitos básicos até implementações práticas, fornecendo uma base sólida para o desenvolvimento web em Java EE.*
