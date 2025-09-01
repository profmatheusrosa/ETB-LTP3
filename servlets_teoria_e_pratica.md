# SERVLETS - TEORIA E PRÁTICA
## Módulo 2 - Java EE

## Sumário

1. [Conteúdo Teórico](#conteúdo-teórico)
    - [Definição de Servlets](#31-definição-de-servlets)
    - [Ciclo de Vida do Servlet](#32-ciclo-de-vida-do-servlet)
    - [Tratamento de Solicitações GET e POST](#33-tratamento-de-solicitações-get-e-post)
    - [Parâmetros e Respostas](#34-parâmetros-e-respostas)
    - [Redirecionamento e Encaminhamento](#35-redirecionamento-e-encaminhamento)
    - [Sessões](#36-sessões)

2. [Conteúdo Prático](#🛠️-conteúdo-prático)
    - [Configuração do Ambiente](#configuração-do-ambiente)
    - [Exercício Prático 1: Primeiro Servlet](#exercício-prático-1-primeiro-servlet)
    - [Exercício Prático 2: Formulário com GET e POST](#exercício-prático-2-formulário-com-get-e-post)
    - [Exercício Prático 3: Sessões e Redirecionamento](#exercício-prático-3-sessões-e-redirecionamento)

3. [Exercícios de Fixação](#📋-exercícios-de-fixação)

4. [Dicas Importantes](#🔧-dicas-importantes)

5. [Resumo dos Conceitos](#📚-resumo-dos-conceitos)

6. [Objetivos de Aprendizagem](#🎯-objetivos-de-aprendizagem)

---


---

## 📚 CONTEÚDO TEÓRICO


## 🎯 OBJETIVOS DE APRENDIZAGEM

Ao final deste módulo, você será capaz de:

✅ Entender o conceito e funcionamento dos Servlets  
✅ Implementar Servlets básicos no NetBeans  
✅ Diferenciar métodos GET e POST  
✅ Trabalhar com parâmetros e respostas  
✅ Implementar redirecionamentos e encaminhamentos  
✅ Gerenciar sessões de usuário  
✅ Criar aplicações web funcionais  

### 3.1 DEFINIÇÃO DE SERVLETS

**O que é um Servlet?**
- Um **Servlet** é uma classe Java que estende a funcionalidade de um servidor web
- Funciona como um "programa" que roda no servidor e responde às requisições dos clientes
- É a base da programação web em Java

**Analogia Simples:**
Imagine um garçom em um restaurante:
- O **cliente** (navegador) faz um pedido (requisição HTTP)
- O **garçom** (Servlet) recebe o pedido, processa e entrega a resposta
- O **restaurante** (servidor web) é onde tudo acontece

**Vantagens dos Servlets:**
- ✅ Performance superior (ficam em memória)
- ✅ Segurança (código roda no servidor)
- ✅ Portabilidade (funcionam em qualquer servidor Java)
- ✅ Reutilização de código

---

### 3.2 CICLO DE VIDA DO SERVLET

O Servlet passa por 3 fases principais:

#### 1. **Inicialização (init)**
```java
public void init() throws ServletException {
    // Executado UMA VEZ quando o Servlet é carregado
    // Aqui você faz configurações iniciais
}
```

#### 2. **Serviço (service)**
```java
public void service(HttpServletRequest request, HttpServletResponse response) {
    // Executado para CADA requisição
    // Aqui você processa as requisições
}
```

#### 3. **Destruição (destroy)**
```java
public void destroy() {
    // Executado quando o Servlet é descarregado
    // Aqui você faz limpeza de recursos
}
```

**Fluxo Completo:**
1. Servidor carrega o Servlet → `init()`
2. Cliente faz requisição → `service()`
3. Servlet processa e responde
4. Servidor descarrega o Servlet → `destroy()`

---

### 3.3 TRATAMENTO DE SOLICITAÇÕES GET E POST

#### **Método GET:**
- **Características:**
  - Dados visíveis na URL
  - Limitado a 2048 caracteres
  - Pode ser cacheado
  - Seguro para leitura

- **Uso:** Buscas, navegação, links

#### **Método POST:**
- **Características:**
  - Dados não aparecem na URL
  - Sem limite de tamanho
  - Não é cacheado
  - Para envio de dados

- **Uso:** Formulários, uploads, dados sensíveis

---

### 3.4 PARÂMETROS E RESPOSTAS

#### **Obtendo Parâmetros:**
```java
// Parâmetros GET (URL)
String nome = request.getParameter("nome");

// Parâmetros POST (formulário)
String email = request.getParameter("email");

// Múltiplos valores
String[] cores = request.getParameterValues("cores");
```

#### **Enviando Respostas:**
```java
// Configurar tipo de conteúdo
response.setContentType("text/html;charset=UTF-8");

// Obter escritor
PrintWriter out = response.getWriter();

// Escrever resposta
out.println("<html><body>");
out.println("<h1>Olá " + nome + "!</h1>");
out.println("</body></html>");
```

---

### 3.5 REDIRECIONAMENTO E ENCAMINHAMENTO

#### **Redirecionamento (sendRedirect):**
```java
// Redireciona para outra URL
response.sendRedirect("pagina.jsp");
```
- **Características:**
  - Nova requisição HTTP
  - URL muda no navegador
  - Perde dados da requisição original

#### **Encaminhamento (forward):**
```java
// Encaminha para outro Servlet/JSP
RequestDispatcher rd = request.getRequestDispatcher("pagina.jsp");
rd.forward(request, response);
```
- **Características:**
  - Mesma requisição HTTP
  - URL não muda
  - Mantém dados da requisição

---

### 3.6 SESSÕES

#### **O que é uma Sessão?**
- É um "encontro" entre cliente e servidor
- Permite armazenar dados do usuário durante sua navegação
- Cada usuário tem sua própria sessão

#### **Criando e Usando Sessões:**
```java
// Obter sessão
HttpSession session = request.getSession();

// Armazenar dados
session.setAttribute("usuario", "João");
session.setAttribute("idade", 25);

// Recuperar dados
String usuario = (String) session.getAttribute("usuario");
Integer idade = (Integer) session.getAttribute("idade");

// Remover dados
session.removeAttribute("idade");

// Invalidar sessão
session.invalidate();
```

---

## 🛠️ CONTEÚDO PRÁTICO

### CONFIGURAÇÃO DO AMBIENTE

#### **1. Instalação do NetBeans:**
1. Baixe o NetBeans IDE (versão 12 ou superior)
2. Durante a instalação, selecione "Java EE" 
3. O WildFly será instalado automaticamente

#### **2. Verificação da Instalação:**
- Abra o NetBeans
- Vá em **Tools → Servers**
- Verifique se o WildFly aparece na lista

---

### EXERCÍCIO PRÁTICO 1: PRIMEIRO SERVLET

#### **Passo a Passo:**

**1. Criar Projeto Web:**
- File → New Project
- Java Web → Web Application
- Nome: `MeuPrimeiroServlet`
- Server: WildFly
- Java EE Version: 8

**2. Criar Servlet:**
- Clique com botão direito no projeto
- New → Servlet
- Nome: `OlaServlet`
- Package: `com.exemplo`

**3. Código do Servlet:**
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

**4. Executar:**
- Clique com botão direito no projeto
- Run
- O navegador abrirá automaticamente
- Acesse: `http://localhost:8080/MeuPrimeiroServlet/ola`

---

### EXERCÍCIO PRÁTICO 2: FORMULÁRIO COM GET E POST

#### **1. Criar Página HTML:**
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

#### **2. Servlet para Cadastro (POST):**
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

#### **3. Servlet para Busca (GET):**
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

---

### EXERCÍCIO PRÁTICO 3: SESSÕES E REDIRECIONAMENTO

#### **1. Página de Login:**
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

#### **2. Servlet de Login:**
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

#### **3. Servlet da Área Restrita:**
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

#### **4. Servlet de Logout:**
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

---

## 📋 EXERCÍCIOS DE FIXAÇÃO

### **Exercício 1: Calculadora Web**
Crie um Servlet que receba dois números e uma operação (+, -, *, /) e retorne o resultado.

### **Exercício 2: Contador de Visitas**
Crie um Servlet que conte quantas vezes foi acessado usando sessão.

### **Exercício 3: Lista de Tarefas**
Crie um sistema simples de lista de tarefas usando sessões para armazenar as tarefas.

---

## 🔧 DICAS IMPORTANTES

### **Configuração do web.xml (Alternativa à Anotação):**
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

### **Tratamento de Erros:**
```java
@Override
protected void doGet(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
    
    try {
        // Seu código aqui
    } catch (Exception e) {
        response.sendError(HttpServletResponse.SC_INTERNAL_SERVER_ERROR, 
                          "Erro interno do servidor");
    }
}
```

### **Configuração de Sessão:**
```java
// Configurar tempo de sessão (em segundos)
HttpSession session = request.getSession();
session.setMaxInactiveInterval(1800); // 30 minutos
```

---

## 📚 RESUMO DOS CONCEITOS

| **Conceito** | **Descrição** | **Exemplo** |
|--------------|---------------|-------------|
| **Servlet** | Classe Java que processa requisições web | `extends HttpServlet` |
| **Ciclo de Vida** | init → service → destroy | Métodos do Servlet |
| **GET** | Requisição para leitura de dados | Links, formulários simples |
| **POST** | Requisição para envio de dados | Formulários complexos |
| **Sessão** | Armazenamento temporário de dados | Carrinho de compras |
| **Redirecionamento** | Nova requisição HTTP | `sendRedirect()` |
| **Encaminhamento** | Mesma requisição HTTP | `forward()` |



