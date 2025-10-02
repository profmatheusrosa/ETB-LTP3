# JAVA WEB E SUPABASE - AULA PRÁTICA E GUIADA
## Módulo 3 - Integração Web + BD com NetBeans, GlassFish e Supabase

## Sumário

1. [Conteúdo Teórico](#conteúdo-teórico)
    - [Conceitos de JDBC com Supabase](#31-conceitos-de-jdbc-com-supabase)
    - [O que é Supabase](#32-o-que-é-supabase)
    - [Conexão com Supabase](#33-conexão-com-supabase)
    - [CRUD Operations](#34-crud-operations)
    - [PreparedStatement](#35-preparedstatement)
    - [Pool de Conexões](#36-pool-de-conexões)
    - [Padrão DAO](#37-padrão-dao)

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

✅ Conectar aplicações Java Web com banco de dados Supabase (PostgreSQL)  
✅ Implementar operações CRUD (Create, Read, Update, Delete)  
✅ Usar PreparedStatement para consultas seguras  
✅ Configurar pool de conexões no GlassFish para Supabase  
✅ Aplicar o padrão DAO (Data Access Object)  
✅ Criar aplicações web completas com persistência na nuvem  
✅ Tratar erros e exceções de banco de dados  
✅ Entender as vantagens do Supabase como Backend-as-a-Service  

---

## 📚 CONTEÚDO TEÓRICO

### 3.1 CONCEITOS DE JDBC COM SUPABASE

**O que é JDBC?**
- **JDBC** (Java Database Connectivity) é uma API padrão para conectar aplicações Java com bancos de dados
- Permite executar comandos SQL e manipular resultados
- **Supabase usa PostgreSQL**, então usamos o driver PostgreSQL JDBC

**Analogia Simples:**
Imagine o JDBC como um "tradutor universal":
- Sua aplicação Java fala "Java"
- O Supabase (PostgreSQL) fala "SQL"
- O JDBC traduz entre os dois idiomas

**Componentes Principais:**
- ✅ **Driver PostgreSQL**: Conecta com o Supabase
- ✅ **Connection**: Representa a conexão com o banco na nuvem
- ✅ **Statement**: Executa comandos SQL
- ✅ **ResultSet**: Armazena resultados das consultas

---

### 3.2 O QUE É SUPABASE

**Definição:**
- **Supabase** é uma plataforma Backend-as-a-Service (BaaS) open-source
- Fornece banco de dados PostgreSQL hospedado na nuvem
- Inclui recursos como autenticação, APIs automáticas e armazenamento de arquivos

**Vantagens do Supabase:**
- ✅ **Gratuito**: Plano gratuito generoso (500MB de banco, 2GB de transferência)
- ✅ **Fácil configuração**: Interface web intuitiva
- ✅ **PostgreSQL**: Banco robusto e confiável
- ✅ **APIs automáticas**: REST e GraphQL automáticas
- ✅ **Interface SQL**: Editor SQL integrado
- ✅ **Real-time**: Atualizações em tempo real
- ✅ **Autenticação**: Sistema de autenticação integrado

**Diferenças do MySQL local:**
- 🏠 **MySQL local**: Requer instalação, configuração, manutenção
- ☁️ **Supabase**: Gerenciado, escalável, sempre disponível

---

### 3.3 CONEXÃO COM SUPABASE

#### **URL de Conexão Supabase:**
```java
String url = "jdbc:postgresql://db.xxxxxxxxxxxx.supabase.co:5432/postgres";
String usuario = "postgres";
String senha = "sua_senha_supabase";
```

#### **Conexão Básica:**
```java
try {
    // Carregar o driver PostgreSQL
    Class.forName("org.postgresql.Driver");
    
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

### 3.4 CRUD OPERATIONS

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

### 3.5 PREPAREDSTATEMENT

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

### 3.6 POOL DE CONEXÕES

**O que é Pool de Conexões?**
- Conjunto de conexões pré-estabelecidas com o banco
- Melhora performance (reutiliza conexões)
- Controla número máximo de conexões simultâneas

**Configuração no GlassFish para Supabase:**
1. **Admin Console** → **Resources** → **JDBC** → **Connection Pools**
2. **New** → **PostgreSQL**
3. Configurar propriedades:
   - Database URL: `jdbc:postgresql://db.xxxxxxxxxxxx.supabase.co:5432/postgres`
   - User: `postgres`
   - Password: `sua_senha_supabase`
   - Initial Pool Size: `5`
   - Maximum Pool Size: `20`

---

### 3.7 PADRÃO DAO

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

#### **1. Configuração do Supabase:**

**Passo 1: Criar Conta no Supabase**
1. Acesse: https://supabase.com
2. Clique em "Start your project"
3. Faça login com GitHub ou crie uma conta
4. Clique em "New Project"

**Passo 2: Configurar Projeto**
1. **Nome do projeto**: `aula-java-web`
2. **Senha do banco**: Crie uma senha forte (salve em local seguro!)
3. **Região**: Escolha a mais próxima (us-east-1 para Brasil)
4. Clique em "Create new project"

**Passo 3: Obter Dados de Conexão**
1. Vá para **Settings** → **Database**
2. Copie as informações:
   - **Host**: `db.xxxxxxxxxxxx.supabase.co`
   - **Database name**: `postgres`
   - **Port**: `5432`
   - **User**: `postgres`
   - **Password**: `sua_senha_escolhida`

**Passo 4: Testar Conexão**
1. Vá para **SQL Editor**
2. Execute o comando:
```sql
SELECT version();
```

#### **2. Configuração do NetBeans:**

**Passo 1: Adicionar Driver PostgreSQL**
1. Baixe o driver PostgreSQL JDBC: https://jdbc.postgresql.org/download/
2. No NetBeans: **Tools** → **Libraries**
3. Clique em **New Library**
4. Nome: `PostgreSQL JDBC Driver`
5. Adicione o arquivo `postgresql-42.6.0.jar`

**Passo 2: Verificar GlassFish**
- Tools → Servers
- Verifique se o GlassFish está configurado
- Se não estiver, adicione o servidor GlassFish

---

### EXERCÍCIO PRÁTICO 1: CONEXÃO BÁSICA

#### **Passo a Passo:**

**1. Criar Projeto Web:**
- File → New Project
- Java Web → Web Application
- Nome: `AulaJavaWebSupabase`
- Server: GlassFish Server
- Java EE Version: 8

**2. Adicionar Dependência PostgreSQL:**
- Clique com botão direito no projeto
- Properties → Libraries
- Add Library → PostgreSQL JDBC Driver

**3. Criar Tabela no Supabase:**
1. Vá para **SQL Editor** no Supabase
2. Execute o comando:
```sql
-- Criar tabela usuarios
CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    idade INTEGER,
    data_cadastro TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Inserir dados de teste
INSERT INTO usuarios (nome, email, idade) VALUES 
('João Silva', 'joao@email.com', 25),
('Maria Santos', 'maria@email.com', 30),
('Pedro Oliveira', 'pedro@email.com', 22);
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
            out.println("<title>Teste de Conexão Supabase</title>");
            out.println("<style>");
            out.println("body { font-family: Arial, sans-serif; margin: 40px; }");
            out.println(".success { color: green; background: #e8f5e8; padding: 10px; border-radius: 5px; }");
            out.println(".error { color: red; background: #ffe8e8; padding: 10px; border-radius: 5px; }");
            out.println(".info { color: blue; background: #e8f4fd; padding: 10px; border-radius: 5px; }");
            out.println("</style>");
            out.println("</head>");
            out.println("<body>");
            out.println("<h2>Teste de Conexão com Supabase</h2>");
            
            try {
                // Carregar driver
                Class.forName("org.postgresql.Driver");
                
                // ⚠️ SUBSTITUA PELOS SEUS DADOS DO SUPABASE
                String url = "jdbc:postgresql://db.xxxxxxxxxxxx.supabase.co:5432/postgres";
                String usuario = "postgres";
                String senha = "SUA_SENHA_SUPABASE";
                
                // Estabelecer conexão
                try (Connection conn = DriverManager.getConnection(url, usuario, senha)) {
                    out.println("<div class='success'>");
                    out.println("<strong>✅ Conexão estabelecida com sucesso!</strong><br>");
                    out.println("Banco: Supabase PostgreSQL<br>");
                    out.println("URL: " + url + "<br>");
                    out.println("Status: Conectado à nuvem ☁️");
                    out.println("</div>");
                    
                    // Testar uma consulta simples
                    try (java.sql.Statement stmt = conn.createStatement();
                         java.sql.ResultSet rs = stmt.executeQuery("SELECT COUNT(*) as total FROM usuarios")) {
                        if (rs.next()) {
                            int total = rs.getInt("total");
                            out.println("<div class='info'>");
                            out.println("<strong>📊 Total de usuários na tabela:</strong> " + total);
                            out.println("</div>");
                        }
                    }
                }
                
            } catch (ClassNotFoundException e) {
                out.println("<div class='error'>");
                out.println("<strong>❌ Erro:</strong> Driver PostgreSQL não encontrado<br>");
                out.println("Verifique se a biblioteca PostgreSQL JDBC foi adicionada ao projeto.");
                out.println("</div>");
            } catch (SQLException e) {
                out.println("<div class='error'>");
                out.println("<strong>❌ Erro de Conexão:</strong> " + e.getMessage() + "<br>");
                out.println("<strong>Possíveis causas:</strong><br>");
                out.println("• URL incorreta do Supabase<br>");
                out.println("• Credenciais incorretas<br>");
                out.println("• Projeto Supabase não está ativo<br>");
                out.println("• Firewall bloqueando a conexão");
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
    <title>Aula Java Web + Supabase</title>
    <meta charset="UTF-8">
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; background: #f5f5f5; }
        .container { max-width: 800px; margin: 0 auto; background: white; padding: 30px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        .header { text-align: center; margin-bottom: 30px; }
        .header h1 { color: #3ecf8e; margin-bottom: 10px; }
        .header p { color: #666; }
        .menu { background: #f8f9fa; padding: 20px; border-radius: 8px; }
        .menu h3 { color: #333; margin-bottom: 15px; }
        .menu a { display: block; margin: 10px 0; padding: 12px 20px; background: #3ecf8e; color: white; text-decoration: none; border-radius: 5px; transition: background 0.3s; }
        .menu a:hover { background: #2ebd7e; }
        .supabase-info { background: #e8f4fd; padding: 15px; border-radius: 5px; margin: 20px 0; border-left: 4px solid #3ecf8e; }
        .supabase-info strong { color: #3ecf8e; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>☁️ Aula Prática: Java Web + Supabase</h1>
            <p>Integração de aplicações Java com banco de dados na nuvem</p>
        </div>
        
        <div class="supabase-info">
            <strong>🚀 Supabase:</strong> Backend-as-a-Service com PostgreSQL hospedado na nuvem
        </div>
        
        <div class="menu">
            <h3>📋 Exercícios Práticos:</h3>
            <a href="teste-conexao">1. 🔗 Teste de Conexão com Supabase</a>
            <a href="listar-usuarios">2. 📊 Listar Usuários</a>
            <a href="formulario-usuario.html">3. ➕ Cadastrar Usuário</a>
            <a href="buscar-usuario.html">4. 🔍 Buscar Usuário</a>
            <a href="dashboard.html">5. 📈 Dashboard</a>
        </div>
        
        <div style="margin-top: 30px; text-align: center; color: #666;">
            <p><strong>Vantagens do Supabase:</strong> Gratuito • Escalável • Sem configuração • APIs automáticas</p>
        </div>
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
    
    // ⚠️ SUBSTITUA PELOS SEUS DADOS DO SUPABASE
    private static final String URL = "jdbc:postgresql://db.xxxxxxxxxxxx.supabase.co:5432/postgres";
    private static final String USUARIO = "postgres";
    private static final String SENHA = "SUA_SENHA_SUPABASE";
    
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
        
        // PostgreSQL usa TIMESTAMP WITH TIME ZONE
        java.sql.Timestamp timestamp = rs.getTimestamp("data_cadastro");
        if (timestamp != null) {
            usuario.setDataCadastro(timestamp.toLocalDateTime());
        }
        
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
            out.println("<title>Lista de Usuários - Supabase</title>");
            out.println("<style>");
            out.println("body { font-family: Arial, sans-serif; margin: 40px; background: #f5f5f5; }");
            out.println(".container { max-width: 1000px; margin: 0 auto; background: white; padding: 30px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }");
            out.println("h2 { color: #3ecf8e; }");
            out.println("table { border-collapse: collapse; width: 100%; margin: 20px 0; }");
            out.println("th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }");
            out.println("th { background-color: #3ecf8e; color: white; }");
            out.println("tr:nth-child(even) { background-color: #f9f9f9; }");
            out.println(".success { color: green; background: #e8f5e8; padding: 10px; border-radius: 5px; margin: 10px 0; }");
            out.println(".error { color: red; background: #ffe8e8; padding: 10px; border-radius: 5px; margin: 10px 0; }");
            out.println(".cloud-info { background: #e8f4fd; padding: 10px; border-radius: 5px; margin: 10px 0; border-left: 4px solid #3ecf8e; }");
            out.println("a { color: #3ecf8e; text-decoration: none; }");
            out.println("a:hover { text-decoration: underline; }");
            out.println("</style>");
            out.println("</head>");
            out.println("<body>");
            out.println("<div class='container'>");
            out.println("<h2>📊 Lista de Usuários</h2>");
            out.println("<div class='cloud-info'>");
            out.println("<strong>☁️ Dados carregados do Supabase PostgreSQL</strong>");
            out.println("</div>");
            
            try {
                UsuarioDAO dao = new UsuarioDAO();
                List<Usuario> usuarios = dao.listarTodos();
                
                if (usuarios.isEmpty()) {
                    out.println("<div class='success'>");
                    out.println("<strong>📝 Nenhum usuário cadastrado.</strong><br>");
                    out.println("Use o formulário de cadastro para adicionar usuários.");
                    out.println("</div>");
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
                    out.println("<div class='success'>");
                    out.println("<strong>✅ Total de usuários:</strong> " + usuarios.size());
                    out.println("</div>");
                }
                
            } catch (SQLException e) {
                out.println("<div class='error'>");
                out.println("<strong>❌ Erro ao conectar com Supabase:</strong><br>");
                out.println(e.getMessage() + "<br>");
                out.println("<strong>Verifique:</strong><br>");
                out.println("• Conexão com internet<br>");
                out.println("• Dados de conexão no UsuarioDAO<br>");
                out.println("• Projeto Supabase ativo");
                out.println("</div>");
            }
            
            out.println("<br><a href='index.html'>🏠 Voltar ao Menu</a>");
            out.println("</div>");
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
    <title>Cadastrar Usuário - Supabase</title>
    <meta charset="UTF-8">
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; background: #f5f5f5; }
        .container { max-width: 500px; margin: 0 auto; background: white; padding: 30px; border-radius: 10px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); }
        h2 { color: #3ecf8e; text-align: center; }
        .cloud-info { background: #e8f4fd; padding: 10px; border-radius: 5px; margin: 10px 0; border-left: 4px solid #3ecf8e; text-align: center; }
        form { margin: 20px 0; }
        label { display: block; margin: 10px 0 5px 0; font-weight: bold; color: #333; }
        input { width: 100%; padding: 10px; margin: 5px 0; border: 1px solid #ddd; border-radius: 5px; box-sizing: border-box; }
        button { background: #3ecf8e; color: white; padding: 12px 30px; border: none; border-radius: 5px; cursor: pointer; width: 100%; font-size: 16px; margin: 10px 0; }
        button:hover { background: #2ebd7e; }
        .error { color: red; background: #ffe8e8; padding: 10px; border-radius: 5px; margin: 10px 0; }
        .success { color: green; background: #e8f5e8; padding: 10px; border-radius: 5px; margin: 10px 0; }
        a { color: #3ecf8e; text-decoration: none; }
        a:hover { text-decoration: underline; }
    </style>
</head>
<body>
    <div class="container">
        <h2>➕ Cadastrar Novo Usuário</h2>
        
        <div class="cloud-info">
            <strong>☁️ Dados serão salvos no Supabase</strong>
        </div>
        
        <form action="CadastrarUsuarioServlet" method="post">
            <label for="nome">Nome Completo:</label>
            <input type="text" id="nome" name="nome" required placeholder="Ex: João Silva">
            
            <label for="email">Email:</label>
            <input type="email" id="email" name="email" required placeholder="Ex: joao@email.com">
            
            <label for="idade">Idade:</label>
            <input type="number" id="idade" name="idade" min="1" max="120" required placeholder="Ex: 25">
            
            <button type="submit">💾 Cadastrar no Supabase</button>
        </form>
        
        <div style="text-align: center; margin-top: 20px;">
            <a href="index.html">🏠 Voltar ao Menu</a>
        </div>
    </div>
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
            // Em caso de erro, redirecionar com parâmetro de erro
            response.sendRedirect("formulario-usuario.html?erro=banco_dados");
        }
    }
}
```

---

## 📋 EXERCÍCIOS DE FIXAÇÃO

### **Exercício 1: Sistema de Produtos**
Crie um sistema completo para gerenciar produtos com:
- Tabela: `produtos` (id, nome, preco, categoria, estoque, data_cadastro)
- CRUD completo (cadastrar, listar, buscar, editar, excluir)
- Formulários web para todas as operações
- Dados salvos no Supabase

### **Exercício 2: Sistema de Vendas**
Crie um sistema de vendas com:
- Tabela: `vendas` (id, cliente, produto, quantidade, valor_total, data_venda)
- Relacionamento com produtos
- Relatório de vendas por período
- Dashboard com estatísticas

### **Exercício 3: Sistema de Login com Supabase**
Implemente autenticação usando Supabase:
- Tabela: `usuarios` (id, login, senha, perfil, ativo)
- Validação de login contra BD
- Controle de sessão baseado em perfil
- Integração com Supabase Auth (opcional)

---

## 🔧 DICAS IMPORTANTES

### **Configuração de Pool de Conexões no GlassFish para Supabase:**

1. **Via Admin Console:**
   - Acesse: http://localhost:4848
   - Resources → JDBC → Connection Pools
   - New → PostgreSQL
   - Nome: `SupabaseConnectionPool`
   - Resource Type: `javax.sql.DataSource`

2. **Propriedades do Pool:**
   ```
   DatabaseName: postgres
   User: postgres
   Password: sua_senha_supabase
   URL: jdbc:postgresql://db.xxxxxxxxxxxx.supabase.co:5432/postgres
   DriverClass: org.postgresql.Driver
   ServerName: db.xxxxxxxxxxxx.supabase.co
   PortNumber: 5432
   ```

3. **Usando JNDI no Código:**
   ```java
   @Resource(name = "jdbc/SupabaseConnectionPool")
   private DataSource dataSource;
   
   private Connection obterConexao() throws SQLException {
       return dataSource.getConnection();
   }
   ```

### **Segurança com Supabase:**

1. **Variáveis de Ambiente:**
```java
// Não hardcode as credenciais!
String url = System.getenv("SUPABASE_URL");
String usuario = System.getenv("SUPABASE_USER");
String senha = System.getenv("SUPABASE_PASSWORD");
```

2. **Row Level Security (RLS):**
```sql
-- Ativar RLS na tabela
ALTER TABLE usuarios ENABLE ROW LEVEL SECURITY;

-- Política de exemplo
CREATE POLICY "Usuários podem ver apenas seus próprios dados" 
ON usuarios FOR SELECT 
USING (auth.uid() = user_id);
```

### **Tratamento de Exceções Específicas do Supabase:**
```java
public class SupabaseException extends Exception {
    public SupabaseException(String message, Throwable cause) {
        super(message, cause);
    }
}

public class UsuarioDAO {
    
    public void inserir(Usuario usuario) throws SupabaseException {
        try {
            // Código de inserção
        } catch (SQLException e) {
            if (e.getMessage().contains("duplicate key")) {
                throw new SupabaseException("Email já cadastrado", e);
            } else if (e.getMessage().contains("connection")) {
                throw new SupabaseException("Erro de conexão com Supabase", e);
            }
            throw new SupabaseException("Erro ao inserir usuário: " + e.getMessage(), e);
        }
    }
}
```

### **Monitoramento e Logs:**
```java
// Adicionar logs para monitoramento
import java.util.logging.Logger;

public class UsuarioDAO {
    private static final Logger logger = Logger.getLogger(UsuarioDAO.class.getName());
    
    public void inserir(Usuario usuario) throws SQLException {
        logger.info("Inserindo usuário: " + usuario.getEmail());
        
        try {
            // Código de inserção
            logger.info("Usuário inserido com sucesso: " + usuario.getEmail());
        } catch (SQLException e) {
            logger.severe("Erro ao inserir usuário: " + e.getMessage());
            throw e;
        }
    }
}
```

---

## 📚 RESUMO DOS CONCEITOS

| **Conceito** | **Descrição** | **Exemplo** |
|--------------|---------------|-------------|
| **Supabase** | Backend-as-a-Service com PostgreSQL | Plataforma na nuvem |
| **JDBC PostgreSQL** | Driver para conectar com PostgreSQL | `org.postgresql.Driver` |
| **PreparedStatement** | Consulta SQL pré-compilada | `PreparedStatement stmt = conn.prepareStatement(sql)` |
| **CRUD** | Create, Read, Update, Delete | Operações básicas de BD |
| **DAO** | Data Access Object | Padrão para acesso a dados |
| **Pool de Conexões** | Conjunto de conexões reutilizáveis | Configuração no servidor |
| **ResultSet** | Resultado de consultas SQL | `ResultSet rs = stmt.executeQuery()` |
| **RLS** | Row Level Security | Segurança a nível de linha |

---

## 🎯 CHECKLIST DE APRENDIZAGEM

### **Conceitos Teóricos:**
- [ ] Entendi o que é Supabase e suas vantagens
- [ ] Compreendi a diferença entre MySQL local e Supabase na nuvem
- [ ] Entendi como usar JDBC com PostgreSQL
- [ ] Compreendi o conceito de Backend-as-a-Service

### **Configuração Prática:**
- [ ] Criei uma conta no Supabase
- [ ] Configurei um projeto no Supabase
- [ ] Obtenho as credenciais de conexão
- [ ] Configurei o driver PostgreSQL no NetBeans

### **Implementação:**
- [ ] Criei conexão básica com o Supabase
- [ ] Implementei operações CRUD completas
- [ ] Usei PreparedStatement para consultas seguras
- [ ] Criei um sistema web funcional com persistência na nuvem

### **Boas Práticas:**
- [ ] Não hardcodei credenciais no código
- [ ] Tratei exceções específicas do Supabase
- [ ] Implementei logs para monitoramento
- [ ] Configurei pool de conexões no GlassFish

---

**🎉 Parabéns! Você agora sabe integrar Java Web com Supabase!**

**Vantagens conquistadas:**
- ✅ Banco de dados na nuvem (sempre disponível)
- ✅ Escalabilidade automática
- ✅ Sem necessidade de instalação local
- ✅ APIs automáticas
- ✅ Interface SQL integrada
- ✅ Planos gratuitos generosos

**Próximos passos sugeridos:**
- Explorar Supabase Auth para autenticação
- Implementar Real-time subscriptions
- Usar Supabase Storage para arquivos
- Aprender sobre Row Level Security (RLS)
- Integrar com APIs REST automáticas do Supabase
