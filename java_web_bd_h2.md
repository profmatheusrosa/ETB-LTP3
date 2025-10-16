# JAVA WEB E BANCO DE DADOS STANDALONE (H2) - AULA PRÁTICA E GUIADA
## Módulo 3 - Integração Web + BD com NetBeans, GlassFish e H2 (embutido)

## Sumário

1. [Conteúdo Teórico](#conteúdo-teórico)
    - [Conceitos de JDBC](#31-conceitos-de-jdbc)
    - [Por que H2?](#32-por-que-h2)
    - [Conexão com Banco de Dados H2](#33-conexão-com-banco-de-dados-h2)
    - [CRUD Operations](#34-crud-operations)
    - [PreparedStatement](#35-preparedstatement)
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

✅ Conectar aplicações Java Web com o banco H2 embutido (standalone)  
✅ Implementar operações CRUD (Create, Read, Update, Delete)  
✅ Usar PreparedStatement para consultas seguras  
✅ Aplicar o padrão DAO (Data Access Object)  
✅ Criar aplicações web completas com persistência de dados  
✅ Testar sem instalar servidor de banco externo  

---

## 📚 CONTEÚDO TEÓRICO

### 3.1 CONCEITOS DE JDBC

**O que é JDBC?**
- **JDBC** (Java Database Connectivity) é uma API padrão para conectar aplicações Java com bancos de dados
- Permite executar comandos SQL e manipular resultados
- Funciona com diversos bancos (H2, MySQL, PostgreSQL, Oracle, etc.)

**Componentes Principais:**
- ✅ **Driver**: Conecta com o banco específico
- ✅ **Connection**: Representa a conexão com o banco
- ✅ **Statement/PreparedStatement**: Executa comandos SQL
- ✅ **ResultSet**: Armazena resultados das consultas

**Boas práticas e recursos (try-with-resources / gerenciamento de conexões)**

- try-with-resources: ao trabalhar com JDBC prefira usar try-with-resources para abrir e fechar automaticamente objetos como `Connection`, `Statement`/`PreparedStatement` e `ResultSet`. Esses objetos implementam `AutoCloseable` e serão fechados automaticamente mesmo se ocorrer uma exceção, evitando vazamentos de recursos e esgotamento de conexões.

- Driver vs DriverManager vs DataSource: o *driver* (`org.h2.Driver`) é a implementação que fala com o banco. `DriverManager` é a forma simples (útil em exemplos e pequenos projetos) para obter `Connection` via `DriverManager.getConnection(url, user, pass)`. Em aplicações mais robustas e em produção use um `DataSource` (com pool de conexões) para melhorar desempenho e gerenciamento das conexões.

---

### 3.2 POR QUE H2?

**Vantagens do H2 (standalone/embutido):**
- ✅ Nenhuma instalação externa (sem XAMPP ou MySQL)  
- ✅ Roda em memória ou em arquivo local  
- ✅ Driver único (`org.h2.Driver`)  
- ✅ Console web opcional para visualizar dados  
- ✅ Excelente para desenvolvimento, testes e aulas  

Modos comuns de uso:
- Em memória: `jdbc:h2:mem:aula;DB_CLOSE_DELAY=-1;MODE=MySQL`
- Em arquivo (na home do usuário): `jdbc:h2:~/aula_java_web;AUTO_SERVER=TRUE;MODE=MySQL`

> Dica: `MODE=MySQL` aproxima a sintaxe do MySQL, facilitando a migração dos exemplos.

---

### 3.3 CONEXÃO COM BANCO DE DADOS H2

#### URL e Credenciais (exemplos):
```java
// Em memória (apaga ao parar a aplicação)
String url = "jdbc:h2:mem:aula;DB_CLOSE_DELAY=-1;MODE=MySQL";
String usuario = "sa";
String senha = "";

// Em arquivo (persiste no disco do usuário)
String urlArquivo = "jdbc:h2:~/aula_java_web;AUTO_SERVER=TRUE;MODE=MySQL";
```

#### Conexão Básica:
```java
try {
    Class.forName("org.h2.Driver");
    Connection conn = DriverManager.getConnection(url, usuario, senha);
    // ... usar a conexão ...
    conn.close();
} catch (ClassNotFoundException | SQLException e) {
    e.printStackTrace();
}
```

---

### 3.4 CRUD OPERATIONS

#### Create (Inserir):
```java
String sql = "INSERT INTO usuarios (nome, email, idade) VALUES (?, ?, ?)";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setString(1, "João Silva");
stmt.setString(2, "joao@email.com");
stmt.setInt(3, 25);
stmt.executeUpdate();
```

#### Read (Consultar):
```java
String sql = "SELECT * FROM usuarios WHERE idade > ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 18);
ResultSet rs = stmt.executeQuery();
while (rs.next()) {
    System.out.println(rs.getString("nome"));
}
```

#### Update (Atualizar):
```java
String sql = "UPDATE usuarios SET idade = ? WHERE id = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 26);
stmt.setInt(2, 1);
stmt.executeUpdate();
```

#### Delete (Excluir):
```java
String sql = "DELETE FROM usuarios WHERE id = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setInt(1, 1);
stmt.executeUpdate();
```

---

### 3.5 PREPAREDSTATEMENT

**Por que usar PreparedStatement?**

✅ Segurança (previne SQL Injection)  
✅ Performance (pré-compilação)  
✅ Legibilidade  

```java
// Vulnerável
String sql = "SELECT * FROM usuarios WHERE nome = '" + nome + "'";

// Seguro
String sql = "SELECT * FROM usuarios WHERE nome = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setString(1, nome);
```

Explicação resumida: além de melhorar a legibilidade, `PreparedStatement` separa o código SQL dos dados, evitando que valores inseridos pelo usuário sejam interpretados como parte do comando SQL (proteção contra SQL Injection). Bancos também podem pré-compilar planos de execução para consultas parametrizadas, trazendo ganho de performance em execuções repetidas.

---

### 3.6 PADRÃO DAO

```java
public interface UsuarioDAO {
    void inserir(Usuario usuario);
    Usuario buscarPorId(int id);
    List<Usuario> listarTodos();
    void atualizar(Usuario usuario);
    void excluir(int id);
}
```

Breve descrição: o padrão DAO (Data Access Object) define uma camada responsável por isolar toda a lógica de acesso a dados da aplicação. Isso significa centralizar operações CRUD em classes específicas (ex.: `UsuarioDAO`), facilitando manutenção, testes e substituição da tecnologia de persistência sem afetar a lógica de negócio.

---

### 3.7 SERVLETS E ANOTAÇÕES

Em aplicações Java Web, servlets são classes que respondem a requisições HTTP. A anotação `@WebServlet` mapeia um servlet para uma URL (ou conjunto de URLs). Exemplo:

```java
@WebServlet(name = "ListarUsuariosServlet", urlPatterns = {"/listar-usuarios"})
public class ListarUsuariosServlet extends HttpServlet { ... }
```

Essa anotação substitui (ou complementa) entradas em `web.xml` e simplifica o mapeamento de rotas. Use `urlPatterns` para definir o caminho que acionará o servlet.

---

## 🛠️ CONTEÚDO PRÁTICO

### CONFIGURAÇÃO DO AMBIENTE

#### 1. Adicionar o Driver H2
1. Baixe o JAR do H2 em `https://h2database.com` (arquivo `h2-*.jar`)
2. NetBeans → Tools → Libraries → New Library → `H2 JDBC Driver`
3. Adicione o JAR do H2 à biblioteca
4. No projeto: Properties → Libraries → Add Library → `H2 JDBC Driver`

Opcional (Maven):
```xml
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <version>2.2.224</version>
  <scope>runtime</scope>
  </dependency>
```

#### 2. Servidor de Aplicação
- Verifique o GlassFish (ou Payara/Tomcat) configurado no NetBeans

---

### EXERCÍCIO PRÁTICO 1: CONEXÃO BÁSICA

#### Criar Projeto Web
- File → New Project → Java Web → Web Application  
- Nome: `AulaJavaWebH2`  
- Server: GlassFish Server  
- Java EE Version: 8  

#### Script de Criação (H2)
```sql
CREATE TABLE IF NOT EXISTS usuarios (
  id INT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  idade INT,
  data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Servlet de Teste de Conexão
```java
package com.aula.web;

import java.io.IOException;
import java.io.PrintWriter;
import java.sql.*;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet(name = "TesteConexaoH2Servlet", urlPatterns = {"/teste-conexao-h2"})
public class TesteConexaoH2Servlet extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
    response.setContentType("text/html;charset=UTF-8");
    try (PrintWriter out = response.getWriter()) {
      out.println("<!DOCTYPE html><html><head><title>Teste H2</title>");
      out.println("<style>body{font-family:Arial;margin:40px}.ok{color:green}.err{color:red}</style>");
      out.println("</head><body><h2>Teste de Conexão com H2</h2>");
      try {
        Class.forName("org.h2.Driver");
        String url = "jdbc:h2:mem:aula;DB_CLOSE_DELAY=-1;MODE=MySQL";
        try (Connection conn = DriverManager.getConnection(url, "sa", "")) {
          try (Statement st = conn.createStatement()) {
            st.executeUpdate("CREATE TABLE IF NOT EXISTS usuarios (" +
              "id INT AUTO_INCREMENT PRIMARY KEY, nome VARCHAR(100), " +
              "email VARCHAR(100) UNIQUE, idade INT, data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP)");
          }
          out.println("<p class='ok'>✅ Conexão H2 OK: " + url + "</p>");
        }
      } catch (Exception e) {
        out.println("<p class='err'>❌ Erro: " + e.getMessage() + "</p>");
      }
      out.println("<br><a href='index.html'>Voltar</a></body></html>");
    }
  }
}
```

#### Página Index
```html
<!DOCTYPE html>
<html>
<head>
  <title>Aula Java Web + H2</title>
  <meta charset="UTF-8">
  <style>
    body{font-family:Arial;margin:40px}
    .menu{background:#f0f0f0;padding:20px;border-radius:5px}
    .menu a{display:block;margin:10px 0;padding:10px;background:#007bff;color:#fff;text-decoration:none;border-radius:3px}
    .menu a:hover{background:#0056b3}
  </style>
  </head>
<body>
  <h1>Aula Prática: Java Web + Banco H2</h1>
  <div class="menu">
    <h3>Exercícios:</h3>
    <a href="teste-conexao-h2">1. Teste de Conexão (H2)</a>
    <a href="listar-usuarios">2. Listar Usuários</a>
    <a href="formulario-usuario.html">3. Cadastrar Usuário</a>
    <a href="buscar-usuario.html">4. Buscar Usuário</a>
  </div>
</body>
</html>
```

---

### EXERCÍCIO PRÁTICO 2: CRUD COMPLETO

#### 1. Classe Usuario (Model)
```java
package com.aula.web.model;

import java.time.LocalDateTime;

public class Usuario {
  private int id;
  private String nome;
  private String email;
  private int idade;
  private LocalDateTime dataCadastro;

  public Usuario() {}
  public Usuario(String nome, String email, int idade) {
    this.nome = nome; this.email = email; this.idade = idade;
  }

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
}
```

#### 2. UsuarioDAO (H2)
```java
package com.aula.web.dao;

import com.aula.web.model.Usuario;
import java.sql.*;
import java.util.*;

public class UsuarioDAO {
  private static final String URL = "jdbc:h2:mem:aula;DB_CLOSE_DELAY=-1;MODE=MySQL";
  private static final String USUARIO = "sa";
  private static final String SENHA = "";

  public UsuarioDAO() {
    try (Connection c = obterConexao(); Statement st = c.createStatement()) {
      st.executeUpdate("CREATE TABLE IF NOT EXISTS usuarios (" +
        "id INT AUTO_INCREMENT PRIMARY KEY, nome VARCHAR(100) NOT NULL, " +
        "email VARCHAR(100) UNIQUE NOT NULL, idade INT, " +
        "data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP)");
    } catch (SQLException e) { throw new RuntimeException(e); }
  }

  private Connection obterConexao() throws SQLException {
    return DriverManager.getConnection(URL, USUARIO, SENHA);
  }

  public void inserir(Usuario usuario) throws SQLException {
    String sql = "INSERT INTO usuarios (nome, email, idade) VALUES (?, ?, ?)";
    try (Connection conn = obterConexao(); PreparedStatement stmt = conn.prepareStatement(sql)) {
      stmt.setString(1, usuario.getNome());
      stmt.setString(2, usuario.getEmail());
      stmt.setInt(3, usuario.getIdade());
      stmt.executeUpdate();
    }
  }

  public Usuario buscarPorId(int id) throws SQLException {
    String sql = "SELECT * FROM usuarios WHERE id = ?";
    try (Connection conn = obterConexao(); PreparedStatement stmt = conn.prepareStatement(sql)) {
      stmt.setInt(1, id);
      try (ResultSet rs = stmt.executeQuery()) { return rs.next() ? criarUsuario(rs) : null; }
    }
  }

  public Usuario buscarPorEmail(String email) throws SQLException {
    String sql = "SELECT * FROM usuarios WHERE email = ?";
    try (Connection conn = obterConexao(); PreparedStatement stmt = conn.prepareStatement(sql)) {
      stmt.setString(1, email);
      try (ResultSet rs = stmt.executeQuery()) { return rs.next() ? criarUsuario(rs) : null; }
    }
  }

  public List<Usuario> listarTodos() throws SQLException {
    String sql = "SELECT * FROM usuarios ORDER BY nome";
    List<Usuario> usuarios = new ArrayList<>();
    try (Connection conn = obterConexao(); PreparedStatement stmt = conn.prepareStatement(sql); ResultSet rs = stmt.executeQuery()) {
      while (rs.next()) usuarios.add(criarUsuario(rs));
    }
    return usuarios;
  }

  public void atualizar(Usuario usuario) throws SQLException {
    String sql = "UPDATE usuarios SET nome = ?, email = ?, idade = ? WHERE id = ?";
    try (Connection conn = obterConexao(); PreparedStatement stmt = conn.prepareStatement(sql)) {
      stmt.setString(1, usuario.getNome());
      stmt.setString(2, usuario.getEmail());
      stmt.setInt(3, usuario.getIdade());
      stmt.setInt(4, usuario.getId());
      stmt.executeUpdate();
    }
  }

  public void excluir(int id) throws SQLException {
    String sql = "DELETE FROM usuarios WHERE id = ?";
    try (Connection conn = obterConexao(); PreparedStatement stmt = conn.prepareStatement(sql)) {
      stmt.setInt(1, id);
      stmt.executeUpdate();
    }
  }

  private Usuario criarUsuario(ResultSet rs) throws SQLException {
    Usuario u = new Usuario();
    u.setId(rs.getInt("id"));
    u.setNome(rs.getString("nome"));
    u.setEmail(rs.getString("email"));
    u.setIdade(rs.getInt("idade"));
    Timestamp ts = rs.getTimestamp("data_cadastro");
    if (ts != null) u.setDataCadastro(ts.toLocalDateTime());
    return u;
  }
}
```

#### 3. Servlet para Listar Usuários
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
      out.println("<!DOCTYPE html><html><head><title>Usuários</title>");
      out.println("<style>body{font-family:Arial;margin:40px}table{border-collapse:collapse;width:100%}th,td{border:1px solid #ddd;padding:8px;text-align:left}th{background:#f2f2f2}</style>");
      out.println("</head><body><h2>Lista de Usuários</h2>");
      try {
        UsuarioDAO dao = new UsuarioDAO();
        List<Usuario> usuarios = dao.listarTodos();
        if (usuarios.isEmpty()) {
          out.println("<p>Nenhum usuário cadastrado.</p>");
        } else {
          out.println("<table><tr><th>ID</th><th>Nome</th><th>Email</th><th>Idade</th><th>Data Cadastro</th></tr>");
          for (Usuario u : usuarios) {
            out.println("<tr><td>"+u.getId()+"</td><td>"+u.getNome()+"</td><td>"+u.getEmail()+"</td><td>"+u.getIdade()+"</td><td>"+u.getDataCadastro()+"</td></tr>");
          }
          out.println("</table>");
        }
      } catch (SQLException e) {
        out.println("<p style='color:red'>Erro: "+e.getMessage()+"</p>");
      }
      out.println("<br><a href='index.html'>Voltar</a></body></html>");
    }
  }
}
```

---

### EXERCÍCIO PRÁTICO 3: SISTEMA DE USUÁRIOS

#### 1. Formulário de Cadastro
```html
<!DOCTYPE html>
<html>
<head>
  <title>Cadastrar Usuário</title>
  <meta charset="UTF-8">
  <style>
    body{font-family:Arial;margin:40px}
    form{background:#f9f9f9;padding:20px;border-radius:5px;max-width:400px}
    input,select{width:100%;padding:8px;margin:5px 0;border:1px solid #ddd;border-radius:3px}
    button{background:#007bff;color:#fff;padding:10px 20px;border:none;border-radius:3px;cursor:pointer}
    button:hover{background:#0056b3}
  </style>
</head>
<body>
  <h2>Cadastrar Novo Usuário</h2>
  <form action="CadastrarUsuarioServlet" method="post">
    <p><label>Nome:</label><input type="text" name="nome" required></p>
    <p><label>Email:</label><input type="email" name="email" required></p>
    <p><label>Idade:</label><input type="number" name="idade" min="1" max="120" required></p>
    <p><button type="submit">Cadastrar</button></p>
  </form>
  <br><a href="index.html">Voltar</a>
</body>
</html>
```

#### 2. Servlet de Cadastro
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
    String nome = request.getParameter("nome");
    String email = request.getParameter("email");
    int idade = Integer.parseInt(request.getParameter("idade"));
    try {
      UsuarioDAO dao = new UsuarioDAO();
      if (dao.buscarPorEmail(email) != null) {
        response.sendRedirect("formulario-usuario.html?erro=email_existe");
        return;
      }
      dao.inserir(new Usuario(nome, email, idade));
      response.sendRedirect("listar-usuarios?sucesso=cadastrado");
    } catch (SQLException e) {
      response.sendRedirect("formulario-usuario.html?erro=banco_dados");
    }
  }
}
```

#### 3. Formulário de Busca
```html
<!DOCTYPE html>
<html>
<head>
  <title>Buscar Usuário</title>
  <meta charset="UTF-8">
  <style>
    body{font-family:Arial;margin:40px}
    form{background:#f9f9f9;padding:20px;border-radius:5px;max-width:400px}
    input{width:100%;padding:8px;margin:5px 0;border:1px solid #ddd;border-radius:3px}
    button{background:#28a745;color:#fff;padding:10px 20px;border:none;border-radius:3px;cursor:pointer}
    button:hover{background:#218838}
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
    <p><label>Termo:</label><input type="text" name="termo" required></p>
    <p><button type="submit">Buscar</button></p>
  </form>
  <br><a href="index.html">Voltar</a>
</body>
</html>
```

#### 4. Servlet de Busca
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
      out.println("<!DOCTYPE html><html><head><title>Busca</title>");
      out.println("<style>body{font-family:Arial;margin:40px}.ok{color:#28a745}.err{color:#dc3545}table{border-collapse:collapse;width:100%}th,td{border:1px solid #ddd;padding:8px;text-align:left}th{background:#f2f2f2}</style>");
      out.println("</head><body><h2>Resultado da Busca</h2>");
      try {
        UsuarioDAO dao = new UsuarioDAO();
        Usuario usuario = null;
        switch (tipo) {
          case "email": usuario = dao.buscarPorEmail(termo); break;
          case "id":
            try { usuario = dao.buscarPorId(Integer.parseInt(termo)); }
            catch (NumberFormatException nfe) {
              out.println("<p class='err'>ID inválido.</p><a href='buscar-usuario.html'>Voltar</a></body></html>");
              return;
            }
            break;
          case "nome":
            out.println("<p class='err'>Busca por nome não implementada.</p>");
            out.println("<a href='buscar-usuario.html'>Nova Busca</a> | <a href='index.html'>Início</a></body></html>");
            return;
        }
        if (usuario != null) {
          out.println("<p class='ok'>✅ Usuário encontrado!</p>");
          out.println("<table><tr><th>Campo</th><th>Valor</th></tr>" +
            "<tr><td>ID</td><td>"+usuario.getId()+"</td></tr>" +
            "<tr><td>Nome</td><td>"+usuario.getNome()+"</td></tr>" +
            "<tr><td>Email</td><td>"+usuario.getEmail()+"</td></tr>" +
            "<tr><td>Idade</td><td>"+usuario.getIdade()+"</td></tr>" +
            "<tr><td>Data Cadastro</td><td>"+usuario.getDataCadastro()+"</td></tr></table>");
        } else {
          out.println("<p class='err'>❌ Usuário não encontrado.</p>");
        }
      } catch (SQLException e) {
        out.println("<p class='err'>Erro BD: "+e.getMessage()+"</p>");
      }
      out.println("<br><a href='buscar-usuario.html'>Nova Busca</a> | <a href='index.html'>Voltar</a></body></html>");
    }
  }
}
```

---

## 📋 EXERCÍCIOS DE FIXAÇÃO

### Exercício 1: Produtos (H2)
Crie CRUD de `produtos` (id, nome, preco, categoria, estoque) usando H2 em memória.

### Exercício 2: Vendas (H2)
Crie `vendas` (id, cliente, produto, quantidade, valor_total, data) e relacione com `produtos`.

### Exercício 3: Login (H2)
Implemente autenticação com tabela `usuarios_login` (id, login, senha, perfil) no H2.

---

## 🔧 DICAS IMPORTANTES

### Console Web do H2 (opcional)
Você pode iniciar o console em desenvolvimento:
```java
org.h2.tools.Server.createWebServer("-webPort", "8082", "-webAllowOthers").start();
// Acesse em: http://localhost:8082  (URL: jdbc:h2:mem:aula ou jdbc:h2:~/aula_java_web)
```

### Migração para MySQL/PostgreSQL
Use `MODE=MySQL` no H2 durante o desenvolvimento para reduzir diferenças de sintaxe.

### Transações
```java
Connection conn = obterConexao();
try {
  conn.setAutoCommit(false);
  // ... operações ...
  conn.commit();
} catch (SQLException e) {
  conn.rollback();
  throw e;
} finally {
  conn.setAutoCommit(true);
  conn.close();
}
```

---

## 📚 RESUMO DOS CONCEITOS

| Conceito | Descrição | Exemplo |
|----------|-----------|---------|
| JDBC | API para conectar Java com BD | `DriverManager.getConnection()` |
| H2 Embutido | Banco standalone em memória/arquivo | `jdbc:h2:mem:aula` |
| PreparedStatement | Consulta SQL segura | `stmt.setString(1, v)` |
| CRUD | Operações básicas | INSERT/SELECT/UPDATE/DELETE |
| DAO | Camada de acesso a dados | `UsuarioDAO` |

---

## 🎯 CHECKLIST DE APRENDIZAGEM

### Conceitos Teóricos
- [ ] Entendi o que é JDBC e H2 embutido
- [ ] Sei configurar URLs H2 em memória e arquivo
- [ ] Compreendi PreparedStatement e DAO

### Implementação Prática
- [ ] Adicionei o driver H2 ao projeto
- [ ] Testei a conexão via Servlet
- [ ] Implementei CRUD completo com H2

### Boas Práticas
- [ ] Usei try-with-resources
- [ ] Tratei exceções de BD adequadamente
- [ ] Organizei o código (DAO/MVC)

---

**🎉 Pronto! Agora você consegue desenvolver e testar Java Web com banco H2 sem depender de instalações externas.**


