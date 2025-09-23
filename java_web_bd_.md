# JAVA WEB E BANCO DE DADOS - AULA PRÁTICA E GUIADA
## Módulo 3 - Integração Web + BD com NetBeans, GlassFish e MySQL

## Sumário

1. [Conteúdo Teórico](#conteúdo-teórico)
    - [Conceitos de JDBC](#31-conceitos-de-jdbc)
    - [Conexão com Banco de Dados](#32-conexão-com-banco-de-dados)
    - [CRUD Operations](#33-crud-operations)
    - [PreparedStatement](#34-preparedstatement)
    - [Pool de Conexões](#35-pool-de-conexões)
    - [Padrão DAO](#36-padrão-dao)

2. [Conteúdo Prático](#🛠️-conteúdo-prático)
    - [Configuração do Ambiente](#configuração-do-ambiente)
    - [Exercício Prático 1: Conexão Básica](#exercício-prático-1-conexão-básica)
    - [Exercício Prático 2: CRUD Completo](#exercício-prático-2-crud-completo)
    - [Exercício Prático 3: Sistema de Usuários](#exercício-prático-3-sistema-de-usuários)

3. [Exercícios de Fixação](#📋-exercícios-de-fixação)

4. [Dicas Importantes](#🔧-dicas-importantes)

5. [Resumo dos Conceitos](#📚-resumo-dos-conceitos)

6. [Objetivos de Aprendizagem](#🎯-objetivos-de-aprendizagem)

---

## 🎯 OBJETIVOS DE APRENDIZAGEM

Ao final deste módulo, você será capaz de:

✅ Conectar aplicações Java Web com banco de dados MySQL  
✅ Implementar operações CRUD (Create, Read, Update, Delete)  
✅ Usar PreparedStatement para consultas seguras  
✅ Configurar pool de conexões no GlassFish  
✅ Aplicar o padrão DAO (Data Access Object)  
✅ Criar aplicações web completas com persistência de dados  
✅ Tratar erros e exceções de banco de dados  

---

## 📚 CONTEÚDO TEÓRICO

### 3.1 CONCEITOS DE JDBC

**O que é JDBC?**
- **JDBC** (Java Database Connectivity) é uma API padrão para conectar aplicações Java com bancos de dados
- Permite executar comandos SQL e manipular resultados
- É independente de banco de dados (funciona com MySQL, PostgreSQL, Oracle, etc.)

**Analogia Simples:**
Imagine o JDBC como um "tradutor universal":
- Sua aplicação Java fala "Java"
- O banco MySQL fala "SQL"
- O JDBC traduz entre os dois idiomas

**Componentes Principais:**
- ✅ **Driver**: Conecta com o banco específico
- ✅ **Connection**: Representa a conexão com o banco
- ✅ **Statement**: Executa comandos SQL
- ✅ **ResultSet**: Armazena resultados das consultas

---

### 3.2 CONEXÃO COM BANCO DE DADOS

#### **URL de Conexão MySQL:**
```java
String url = "jdbc:mysql://localhost:3306/nome_do_banco";
String usuario = "root";
String senha = "sua_senha";
```

#### **Conexão Básica:**
```java
try {
    // Carregar o driver
    Class.forName("com.mysql.cj.jdbc.Driver");
    
    // Estabelecer conexão
    Connection conn = DriverManager.getConnection(url, usuario, senha);
    
    // Usar a conexão...
    
    // Fechar conexão
    conn.close();
} catch (ClassNotFoundException | SQLException e) {
    e.printStackTrace();
}
```

---

### 3.3 CRUD OPERATIONS

#### **Create (Inserir):**
```java
String sql = "INSERT INTO usuarios (nome, email, idade) VALUES (?, ?, ?)";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setString(1, "João Silva");
stmt.setString(2, "joao@email.com");
stmt.setInt(3, 25);
stmt.executeUpdate();
```

#### **Read (Consultar):**
```java
String sql = "SELECT * FROM usuarios WHERE idade > ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 18);
ResultSet rs = stmt.executeQuery();

while (rs.next()) {
    String nome = rs.getString("nome");
    String email = rs.getString("email");
    int idade = rs.getInt("idade");
    System.out.println(nome + " - " + email + " - " + idade);
}
```

#### **Update (Atualizar):**
```java
String sql = "UPDATE usuarios SET idade = ? WHERE id = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 26);
stmt.setInt(2, 1);
stmt.executeUpdate();
```

#### **Delete (Excluir):**
```java
String sql = "DELETE FROM usuarios WHERE id = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 1);
stmt.executeUpdate();
```

---

### 3.4 PREPAREDSTATEMENT

**Por que usar PreparedStatement?**

✅ **Segurança**: Previne SQL Injection  
✅ **Performance**: Comandos são pré-compilados  
✅ **Legibilidade**: Código mais limpo  

**Exemplo de SQL Injection:**
```java
// PERIGOSO - Vulnerável a SQL Injection
String sql = "SELECT * FROM usuarios WHERE nome = '" + nome + "'";

// SEGURO - Usando PreparedStatement
String sql = "SELECT * FROM usuarios WHERE nome = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setString(1, nome);
```

---

### 3.5 POOL DE CONEXÕES

**O que é Pool de Conexões?**
- Conjunto de conexões pré-estabelecidas com o banco
- Melhora performance (reutiliza conexões)
- Controla número máximo de conexões simultâneas

**Configuração no GlassFish:**
1. **Admin Console** → **Resources** → **JDBC** → **Connection Pools**
2. **New** → **MySQL**
3. Configurar propriedades:
   - Database URL: `jdbc:mysql://localhost:3306/seu_banco`
   - User: `root`
   - Password: `sua_senha`
   - Initial Pool Size: `5`
   - Maximum Pool Size: `20`

---

### 3.6 PADRÃO DAO

**O que é DAO (Data Access Object)?**
- Padrão de projeto que separa lógica de acesso a dados
- Cada entidade tem seu próprio DAO
- Facilita manutenção e testes

**Estrutura Básica:**
```java
public interface UsuarioDAO {
    void inserir(Usuario usuario);
    Usuario buscarPorId(int id);
    List<Usuario> listarTodos();
    void atualizar(Usuario usuario);
    void excluir(int id);
}

public class UsuarioDAOImpl implements UsuarioDAO {
    // Implementação dos métodos
}
```

---

## 🛠️ CONTEÚDO PRÁTICO

### CONFIGURAÇÃO DO AMBIENTE

#### **1. Instalação do XAMPP:**
1. Baixe o XAMPP em: https://www.apachefriends.org/
2. Instale e inicie o Apache + MySQL
3. Acesse: http://localhost/phpmyadmin
4. Crie um banco chamado `aula_java_web`

#### **2. Configuração do NetBeans:**
1. Abra o NetBeans IDE
2. Vá em **Tools → Libraries**
3. Clique em **New Library**
4. Nome: `MySQL JDBC Driver`
5. Adicione o arquivo `mysql-connector-java-8.0.33.jar`

#### **3. Verificação do GlassFish:**
- Tools → Servers
- Verifique se o GlassFish está configurado
- Se não estiver, adicione o servidor GlassFish

---

### EXERCÍCIO PRÁTICO 1: CONEXÃO BÁSICA

#### **Passo a Passo:**

**1. Criar Projeto Web:**
- File → New Project
- Java Web → Web Application
- Nome: `AulaJavaWebBD`
- Server: GlassFish Server
- Java EE Version: 8

**2. Adicionar Dependência MySQL:**
- Clique com botão direito no projeto
- Properties → Libraries
- Add Library → MySQL JDBC Driver

**3. Criar Tabela no MySQL:**
```sql
CREATE DATABASE aula_java_web;
USE aula_java_web;

CREATE TABLE usuarios (
    id INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    idade INT,
    data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

**4. Servlet de Teste de Conexão:**
```java
package com.aula.web;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "TesteConexaoServlet", urlPatterns = {"/teste-conexao"})
public class TesteConexaoServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        response.setContentType("text/html;charset=UTF-8");
        
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Teste de Conexão</title>");
            out.println("<style>");
            out.println("body { font-family: Arial, sans-serif; margin: 40px; }");
            out.println(".success { color: green; background: #e8f5e8; padding: 10px; border-radius: 5px; }");
            out.println(".error { color: red; background: #ffe8e8; padding: 10px; border-radius: 5px; }");
            out.println("</style>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Teste de Conexão com MySQL</h2>");
            
            try {
                // Carregar driver
                Class.forName("com.mysql.cj.jdbc.Driver");
                
                // Configurações de conexão
                String url = "jdbc:mysql://localhost:3306/aula_java_web";
                String usuario = "root";
                String senha = ""; // Senha do seu MySQL
                
                // Estabelecer conexão
                try (Connection conn = DriverManager.getConnection(url, usuario, senha)) {
                    out.println("<div class='success'>");
                    out.println("<strong>✅ Conexão estabelecida com sucesso!</strong><br>");
                    out.println("Banco: aula_java_web<br>");
                    out.println("URL: " + url);
                    out.println("</div>");
                }
                
            } catch (ClassNotFoundException e) {
                out.println("<div class='error'>");
                out.println("<strong>❌ Erro:</strong> Driver MySQL não encontrado<br>");
                out.println("Verifique se a biblioteca MySQL JDBC foi adicionada ao projeto.");
                out.println("</div>");
            } catch (SQLException e) {
                out.println("<div class='error'>");
                out.println("<strong>❌ Erro de Conexão:</strong> " + e.getMessage() + "<br>");
                out.println("Verifique se o MySQL está rodando e as credenciais estão corretas.");
                out.println("</div>");
            }
            
            out.println("<br><a href='index.html'>Voltar</a>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

**5. Página Index:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Aula Java Web + BD</title>
    <meta charset="UTF-8">
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        .menu { background: #f0f0f0; padding: 20px; border-radius: 5px; }
        .menu a { display: block; margin: 10px 0; padding: 10px; background: #007bff; color: white; text-decoration: none; border-radius: 3px; }
        .menu a:hover { background: #0056b3; }
    </style>
</head>
<body>
    <h1>Aula Prática: Java Web + Banco de Dados</h1>
    
    <div class="menu">
        <h3>Exercícios:</h3>
        <a href="teste-conexao">1. Teste de Conexão</a>
        <a href="listar-usuarios">2. Listar Usuários</a>
        <a href="formulario-usuario.html">3. Cadastrar Usuário</a>
        <a href="buscar-usuario.html">4. Buscar Usuário</a>
    </div>
</body>
</html>
```

---

### EXERCÍCIO PRÁTICO 2: CRUD COMPLETO

#### **1. Classe Usuario (Model):**
```java
package com.aula.web.model;

import java.time.LocalDateTime;

public class Usuario {
    private int id;
    private String nome;
    private String email;
    private int idade;
    private LocalDateTime dataCadastro;
    
    // Construtores
    public Usuario() {}
    
    public Usuario(String nome, String email, int idade) {
        this.nome = nome;
        this.email = email;
        this.idade = idade;
    }
    
    // Getters e Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    
    public String getNome() { return nome; }
    public void setNome(String nome) { this.nome = nome; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public int getIdade() { return idade; }
    public void setIdade(int idade) { this.idade = idade; }
    
    public LocalDateTime getDataCadastro() { return dataCadastro; }
    public void setDataCadastro(LocalDateTime dataCadastro) { this.dataCadastro = dataCadastro; }
    
    @Override
    public String toString() {
        return "Usuario{id=" + id + ", nome='" + nome + "', email='" + email + "', idade=" + idade + "}";
    }
}
```

#### **2. UsuarioDAO (Data Access Object):**
```java
package com.aula.web.dao;

import com.aula.web.model.Usuario;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class UsuarioDAO {
    
    private static final String URL = "jdbc:mysql://localhost:3306/aula_java_web";
    private static final String USUARIO = "root";
    private static final String SENHA = ""; // Sua senha do MySQL
    
    // Método para obter conexão
    private Connection obterConexao() throws SQLException {
        return DriverManager.getConnection(URL, USUARIO, SENHA);
    }
    
    // Inserir usuário
    public void inserir(Usuario usuario) throws SQLException {
        String sql = "INSERT INTO usuarios (nome, email, idade) VALUES (?, ?, ?)";
        
        try (Connection conn = obterConexao();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setString(1, usuario.getNome());
            stmt.setString(2, usuario.getEmail());
            stmt.setInt(3, usuario.getIdade());
            
            stmt.executeUpdate();
        }
    }
    
    // Buscar por ID
    public Usuario buscarPorId(int id) throws SQLException {
        String sql = "SELECT * FROM usuarios WHERE id = ?";
        
        try (Connection conn = obterConexao();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setInt(1, id);
            
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    return criarUsuario(rs);
                }
            }
        }
        return null;
    }
    
    // Buscar por email
    public Usuario buscarPorEmail(String email) throws SQLException {
        String sql = "SELECT * FROM usuarios WHERE email = ?";
        
        try (Connection conn = obterConexao();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setString(1, email);
            
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    return criarUsuario(rs);
                }
            }
        }
        return null;
    }
    
    // Listar todos
    public List<Usuario> listarTodos() throws SQLException {
        String sql = "SELECT * FROM usuarios ORDER BY nome";
        List<Usuario> usuarios = new ArrayList<>();
        
        try (Connection conn = obterConexao();
             PreparedStatement stmt = conn.prepareStatement(sql);
             ResultSet rs = stmt.executeQuery()) {
            
            while (rs.next()) {
                usuarios.add(criarUsuario(rs));
            }
        }
        return usuarios;
    }
    
    // Atualizar
    public void atualizar(Usuario usuario) throws SQLException {
        String sql = "UPDATE usuarios SET nome = ?, email = ?, idade = ? WHERE id = ?";
        
        try (Connection conn = obterConexao();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setString(1, usuario.getNome());
            stmt.setString(2, usuario.getEmail());
            stmt.setInt(3, usuario.getIdade());
            stmt.setInt(4, usuario.getId());
            
            stmt.executeUpdate();
        }
    }
    
    // Excluir
    public void excluir(int id) throws SQLException {
        String sql = "DELETE FROM usuarios WHERE id = ?";
        
        try (Connection conn = obterConexao();
             PreparedStatement stmt = conn.prepareStatement(sql)) {
            
            stmt.setInt(1, id);
            stmt.executeUpdate();
        }
    }
    
    // Método auxiliar para criar objeto Usuario a partir do ResultSet
    private Usuario criarUsuario(ResultSet rs) throws SQLException {
        Usuario usuario = new Usuario();
        usuario.setId(rs.getInt("id"));
        usuario.setNome(rs.getString("nome"));
        usuario.setEmail(rs.getString("email"));
        usuario.setIdade(rs.getInt("idade"));
        usuario.setDataCadastro(rs.getTimestamp("data_cadastro").toLocalDateTime());
        return usuario;
    }
}
```

#### **3. Servlet para Listar Usuários:**
```java
package com.aula.web;

import com.aula.web.dao.UsuarioDAO;
import com.aula.web.model.Usuario;
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.SQLException;
import java.util.List;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "ListarUsuariosServlet", urlPatterns = {"/listar-usuarios"})
public class ListarUsuariosServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        response.setContentType("text/html;charset=UTF-8");
        
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Lista de Usuários</title>");
            out.println("<style>");
            out.println("body { font-family: Arial, sans-serif; margin: 40px; }");
            out.println("table { border-collapse: collapse; width: 100%; }");
            out.println("th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }");
            out.println("th { background-color: #f2f2f2; }");
            out.println(".success { color: green; }");
            out.println(".error { color: red; }");
            out.println("</style>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Lista de Usuários</h2>");
            
            try {
                UsuarioDAO dao = new UsuarioDAO();
                List<Usuario> usuarios = dao.listarTodos();
                
                if (usuarios.isEmpty()) {
                    out.println("<p>Nenhum usuário cadastrado.</p>");
                } else {
                    out.println("<table>");
                    out.println("<tr><th>ID</th><th>Nome</th><th>Email</th><th>Idade</th><th>Data Cadastro</th></tr>");
                    
                    for (Usuario usuario : usuarios) {
                        out.println("<tr>");
                        out.println("<td>" + usuario.getId() + "</td>");
                        out.println("<td>" + usuario.getNome() + "</td>");
                        out.println("<td>" + usuario.getEmail() + "</td>");
                        out.println("<td>" + usuario.getIdade() + "</td>");
                        out.println("<td>" + usuario.getDataCadastro() + "</td>");
                        out.println("</tr>");
                    }
                    
                    out.println("</table>");
                }
                
            } catch (SQLException e) {
                out.println("<div class='error'>");
                out.println("<strong>Erro ao listar usuários:</strong> " + e.getMessage());
                out.println("</div>");
            }
            
            out.println("<br><a href='index.html'>Voltar</a>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

---

### EXERCÍCIO PRÁTICO 3: SISTEMA DE USUÁRIOS

#### **1. Formulário de Cadastro:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Cadastrar Usuário</title>
    <meta charset="UTF-8">
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        form { background: #f9f9f9; padding: 20px; border-radius: 5px; max-width: 400px; }
        input, select { width: 100%; padding: 8px; margin: 5px 0; border: 1px solid #ddd; border-radius: 3px; }
        button { background: #007bff; color: white; padding: 10px 20px; border: none; border-radius: 3px; cursor: pointer; }
        button:hover { background: #0056b3; }
        .error { color: red; }
        .success { color: green; }
    </style>
</head>
<body>
    <h2>Cadastrar Novo Usuário</h2>
    
    <form action="CadastrarUsuarioServlet" method="post">
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
            <input type="number" name="idade" min="1" max="120" required>
        </p>
        <p>
            <button type="submit">Cadastrar</button>
        </p>
    </form>
    
    <br><a href="index.html">Voltar</a>
</body>
</html>
```

#### **2. Servlet de Cadastro:**
```java
package com.aula.web;

import com.aula.web.dao.UsuarioDAO;
import com.aula.web.model.Usuario;
import java.io.IOException;
import java.sql.SQLException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "CadastrarUsuarioServlet", urlPatterns = {"/CadastrarUsuarioServlet"})
public class CadastrarUsuarioServlet extends HttpServlet {

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        // Obter parâmetros do formulário
        String nome = request.getParameter("nome");
        String email = request.getParameter("email");
        int idade = Integer.parseInt(request.getParameter("idade"));
        
        try {
            UsuarioDAO dao = new UsuarioDAO();
            
            // Verificar se email já existe
            Usuario usuarioExistente = dao.buscarPorEmail(email);
            if (usuarioExistente != null) {
                response.sendRedirect("formulario-usuario.html?erro=email_existe");
                return;
            }
            
            // Criar novo usuário
            Usuario novoUsuario = new Usuario(nome, email, idade);
            dao.inserir(novoUsuario);
            
            // Redirecionar para lista com sucesso
            response.sendRedirect("listar-usuarios?sucesso=cadastrado");
            
        } catch (SQLException e) {
            response.sendRedirect("formulario-usuario.html?erro=banco_dados");
        }
    }
}
```

#### **3. Formulário de Busca:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Buscar Usuário</title>
    <meta charset="UTF-8">
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        form { background: #f9f9f9; padding: 20px; border-radius: 5px; max-width: 400px; }
        input { width: 100%; padding: 8px; margin: 5px 0; border: 1px solid #ddd; border-radius: 3px; }
        button { background: #28a745; color: white; padding: 10px 20px; border: none; border-radius: 3px; cursor: pointer; }
        button:hover { background: #218838; }
        .resultado { background: #e9ecef; padding: 15px; border-radius: 5px; margin: 20px 0; }
        .nao-encontrado { color: #dc3545; }
        .encontrado { color: #28a745; }
    </style>
</head>
<body>
    <h2>Buscar Usuário</h2>
    
    <form action="BuscarUsuarioServlet" method="get">
        <p>
            <label>Buscar por:</label>
            <select name="tipo">
                <option value="email">Email</option>
                <option value="nome">Nome</option>
                <option value="id">ID</option>
            </select>
        </p>
        <p>
            <label>Termo de busca:</label>
            <input type="text" name="termo" required>
        </p>
        <p>
            <button type="submit">Buscar</button>
        </p>
    </form>
    
    <br><a href="index.html">Voltar</a>
</body>
</html>
```

#### **4. Servlet de Busca:**
```java
package com.aula.web;

import com.aula.web.dao.UsuarioDAO;
import com.aula.web.model.Usuario;
import java.io.IOException;
import java.io.PrintWriter;
import java.sql.SQLException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "BuscarUsuarioServlet", urlPatterns = {"/BuscarUsuarioServlet"})
public class BuscarUsuarioServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        String tipo = request.getParameter("tipo");
        String termo = request.getParameter("termo");
        
        response.setContentType("text/html;charset=UTF-8");
        
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html>");
            out.println("<html>");
            out.println("<head>");
            out.println("<title>Resultado da Busca</title>");
            out.println("<style>");
            out.println("body { font-family: Arial, sans-serif; margin: 40px; }");
            out.println(".resultado { background: #e9ecef; padding: 15px; border-radius: 5px; margin: 20px 0; }");
            out.println(".encontrado { color: #28a745; border: 2px solid #28a745; }");
            out.println(".nao-encontrado { color: #dc3545; border: 2px solid #dc3545; }");
            out.println("table { border-collapse: collapse; width: 100%; }");
            out.println("th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }");
            out.println("th { background-color: #f2f2f2; }");
            out.println("</style>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Resultado da Busca</h2>");
            
            try {
                UsuarioDAO dao = new UsuarioDAO();
                Usuario usuario = null;
                
                switch (tipo) {
                    case "email":
                        usuario = dao.buscarPorEmail(termo);
                        break;
                    case "id":
                        try {
                            int id = Integer.parseInt(termo);
                            usuario = dao.buscarPorId(id);
                        } catch (NumberFormatException e) {
                            out.println("<div class='resultado nao-encontrado'>");
                            out.println("<strong>Erro:</strong> ID deve ser um número válido.");
                            out.println("</div>");
                            out.println("<br><a href='buscar-usuario.html'>Nova Busca</a> | <a href='index.html'>Voltar</a>");
                            out.println("</body></html>");
                            return;
                        }
                        break;
                    case "nome":
                        // Implementar busca por nome se necessário
                        out.println("<div class='resultado nao-encontrado'>");
                        out.println("<strong>Busca por nome ainda não implementada.</strong>");
                        out.println("</div>");
                        out.println("<br><a href='buscar-usuario.html'>Nova Busca</a> | <a href='index.html'>Voltar</a>");
                        out.println("</body></html>");
                        return;
                }
                
                if (usuario != null) {
                    out.println("<div class='resultado encontrado'>");
                    out.println("<strong>✅ Usuário encontrado!</strong>");
                    out.println("</div>");
                    
                    out.println("<table>");
                    out.println("<tr><th>Campo</th><th>Valor</th></tr>");
                    out.println("<tr><td>ID</td><td>" + usuario.getId() + "</td></tr>");
                    out.println("<tr><td>Nome</td><td>" + usuario.getNome() + "</td></tr>");
                    out.println("<tr><td>Email</td><td>" + usuario.getEmail() + "</td></tr>");
                    out.println("<tr><td>Idade</td><td>" + usuario.getIdade() + "</td></tr>");
                    out.println("<tr><td>Data Cadastro</td><td>" + usuario.getDataCadastro() + "</td></tr>");
                    out.println("</table>");
                } else {
                    out.println("<div class='resultado nao-encontrado'>");
                    out.println("<strong>❌ Usuário não encontrado.</strong><br>");
                    out.println("Termo buscado: <strong>" + termo + "</strong><br>");
                    out.println("Tipo de busca: <strong>" + tipo + "</strong>");
                    out.println("</div>");
                }
                
            } catch (SQLException e) {
                out.println("<div class='resultado nao-encontrado'>");
                out.println("<strong>Erro de banco de dados:</strong> " + e.getMessage());
                out.println("</div>");
            }
            
            out.println("<br><a href='buscar-usuario.html'>Nova Busca</a> | <a href='index.html'>Voltar</a>");
            out.println("</body>");
            out.println("</html>");
        }
    }
}
```

---

## 📋 EXERCÍCIOS DE FIXAÇÃO

### **Exercício 1: Sistema de Produtos**
Crie um sistema completo para gerenciar produtos com:
- Tabela: `produtos` (id, nome, preco, categoria, estoque)
- CRUD completo (cadastrar, listar, buscar, editar, excluir)
- Formulários web para todas as operações

### **Exercício 2: Sistema de Vendas**
Crie um sistema de vendas com:
- Tabela: `vendas` (id, cliente, produto, quantidade, valor_total, data)
- Relacionamento com produtos
- Relatório de vendas por período

### **Exercício 3: Sistema de Login com BD**
Implemente autenticação usando banco de dados:
- Tabela: `usuarios` (id, login, senha, perfil)
- Validação de login contra BD
- Controle de sessão baseado em perfil

---

## 🔧 DICAS IMPORTANTES

### **Configuração de Pool de Conexões no GlassFish:**

1. **Via Admin Console:**
   - Acesse: http://localhost:4848
   - Resources → JDBC → Connection Pools
   - New → MySQL
   - Nome: `AulaJavaWebPool`
   - Resource Type: `javax.sql.DataSource`

2. **Propriedades do Pool:**
   ```
   DatabaseName: aula_java_web
   User: root
   Password: sua_senha
   URL: jdbc:mysql://localhost:3306/aula_java_web
   DriverClass: com.mysql.cj.jdbc.Driver
   ```

3. **Usando JNDI no Código:**
   ```java
   @Resource(name = "jdbc/AulaJavaWebPool")
   private DataSource dataSource;
   
   private Connection obterConexao() throws SQLException {
       return dataSource.getConnection();
   }
   ```

### **Tratamento de Exceções:**
```java
public class UsuarioDAO {
    
    public void inserir(Usuario usuario) throws DAOException {
        try {
            // Código de inserção
        } catch (SQLException e) {
            throw new DAOException("Erro ao inserir usuário: " + e.getMessage(), e);
        }
    }
}
```

### **Transações:**
```java
Connection conn = null;
try {
    conn = obterConexao();
    conn.setAutoCommit(false);
    
    // Múltiplas operações
    
    conn.commit();
} catch (SQLException e) {
    if (conn != null) {
        conn.rollback();
    }
    throw e;
} finally {
    if (conn != null) {
        conn.setAutoCommit(true);
        conn.close();
    }
}
```

---

## 📚 RESUMO DOS CONCEITOS

| **Conceito** | **Descrição** | **Exemplo** |
|--------------|---------------|-------------|
| **JDBC** | API para conectar Java com BD | `DriverManager.getConnection()` |
| **PreparedStatement** | Consulta SQL pré-compilada | `PreparedStatement stmt = conn.prepareStatement(sql)` |
| **CRUD** | Create, Read, Update, Delete | Operações básicas de BD |
| **DAO** | Data Access Object | Padrão para acesso a dados |
| **Pool de Conexões** | Conjunto de conexões reutilizáveis | Configuração no servidor |
| **ResultSet** | Resultado de consultas SQL | `ResultSet rs = stmt.executeQuery()` |
| **JNDI** | Java Naming and Directory Interface | `@Resource(name = "jdbc/Pool")` |

---

## 🎯 CHECKLIST DE APRENDIZAGEM

### **Conceitos Teóricos:**
- [ ] Entendi o que é JDBC e como funciona
- [ ] Compreendi a diferença entre Statement e PreparedStatement
- [ ] Entendi o padrão DAO e suas vantagens
- [ ] Compreendi o conceito de pool de conexões

### **Implementação Prática:**
- [ ] Configurei o ambiente (NetBeans + GlassFish + MySQL)
- [ ] Criei conexão básica com o banco de dados
- [ ] Implementei operações CRUD completas
- [ ] Usei PreparedStatement para consultas seguras
- [ ] Criei um sistema web funcional com persistência

### **Boas Práticas:**
- [ ] Tratei exceções de banco de dados adequadamente
- [ ] Usei try-with-resources para fechar conexões
- [ ] Implementei validações nos formulários
- [ ] Organizei o código seguindo padrões (DAO, MVC)

---

**🎉 Parabéns! Você agora sabe integrar Java Web com banco de dados MySQL!**

**Próximos passos sugeridos:**
- Aprender sobre JPA/Hibernate para ORM
- Implementar padrão MVC com Servlets
- Trabalhar com relacionamentos entre tabelas
- Implementar validações mais robustas
