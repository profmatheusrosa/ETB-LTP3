# EXPLICAÇÃO DETALHADA – PRÁTICAS JAVA WEB + H2 (Módulo 3)

## Índice

1. [Visão Geral e Objetivos](#visao-geral)
2. [Configuração do Ambiente](#configuracao)
3. [Exercício Prático 1: Conexão Básica com H2](#ex1)
4. [Exercício Prático 2: CRUD Completo (Model, DAO, Servlet)](#ex2)
5. [Exercício Prático 3: Sistema de Usuários (Cadastro e Busca)](#ex3)
6. [Boas Práticas, Erros Comuns e Dicas](#boas-praticas)
7. [Checklist de Aprendizagem](#checklist)

---

## Visão Geral e Objetivos <a id="visao-geral"></a>

Este guia detalha, passo a passo, as práticas com banco de dados H2 em aplicações Java Web (Servlets/JSP), no mesmo estilo do documento de JSP. O foco é permitir que você:

- Configure o H2 embutido (memória/arquivo) rapidamente
- Teste a conexão via Servlet
- Implemente CRUD completo usando JDBC com `PreparedStatement`
- Estruture o acesso a dados com o padrão DAO
- Construa um mini sistema web de usuários

---

## Configuração do Ambiente <a id="configuracao"></a>

### Driver H2

- Baixe o JAR em `https://h2database.com` e adicione ao projeto, ou use Maven:
```xml
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <version>2.2.224</version>
  <scope>runtime</scope>
</dependency>
```

### URLs de Conexão (exemplos)
```java
// Em memória (apaga ao parar a aplicação)
String urlMem = "jdbc:h2:mem:aula;DB_CLOSE_DELAY=-1;MODE=MySQL";

// Em arquivo (persiste na pasta do usuário)
String urlArquivo = "jdbc:h2:~/aula_java_web;AUTO_SERVER=TRUE;MODE=MySQL";

String usuario = "sa";
String senha = "";
```

> Observação: `MODE=MySQL` aproxima a sintaxe do MySQL, facilitando a migração.

---

## Exercício Prático 1: Conexão Básica com H2 <a id="ex1"></a>

### Objetivo
Criar um Servlet que valida a conexão, cria a tabela `usuarios` (se não existir) e exibe o resultado.

### Código de Referência (Servlet de Teste)
```java
@WebServlet(name = "TesteConexaoH2Servlet", urlPatterns = {"/teste-conexao-h2"})
public class TesteConexaoH2Servlet extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
    response.setContentType("text/html;charset=UTF-8");
    try (PrintWriter out = response.getWriter()) {
      out.println("<!DOCTYPE html><html><head><title>Teste H2</title></head><body>");
      out.println("<h2>Teste de Conexão com H2</h2>");
      try {
        Class.forName("org.h2.Driver");
        String url = "jdbc:h2:mem:aula;DB_CLOSE_DELAY=-1;MODE=MySQL";
        try (Connection conn = DriverManager.getConnection(url, "sa", "")) {
          try (Statement st = conn.createStatement()) {
            st.executeUpdate("CREATE TABLE IF NOT EXISTS usuarios (" +
              "id INT AUTO_INCREMENT PRIMARY KEY, " +
              "nome VARCHAR(100), email VARCHAR(100) UNIQUE, idade INT, " +
              "data_cadastro TIMESTAMP DEFAULT CURRENT_TIMESTAMP)");
          }
          out.println("<p class='ok'>Conexão H2 OK: " + url + "</p>");
        }
      } catch (Exception e) {
        out.println("<p class='err'>Erro: " + e.getMessage() + "</p>");
      }
      out.println("<a href='index.html'>Voltar</a></body></html>");
    }
  }
}
```

### Pontos-Chave
- `Class.forName("org.h2.Driver")`: carrega o driver.
- URL em memória com `DB_CLOSE_DELAY=-1`: mantém o banco vivo enquanto a JVM estiver ativa.
- Criação idempotente da tabela com `CREATE TABLE IF NOT EXISTS`.

---

## Exercício Prático 2: CRUD Completo (Model, DAO, Servlet) <a id="ex2"></a>

### Objetivo
Modelar a entidade `Usuario`, criar um `UsuarioDAO` com operações CRUD via JDBC e um Servlet para listar usuários.

### 1) Model `Usuario`
```java
public class Usuario {
  private int id;
  private String nome;
  private String email;
  private int idade;
  private LocalDateTime dataCadastro;
  // Getters/Setters e construtores
}
```

### 2) DAO (H2 + JDBC + PreparedStatement)
```java
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

### 3) Servlet para Listar Usuários
```java
@WebServlet(name = "ListarUsuariosServlet", urlPatterns = {"/listar-usuarios"})
public class ListarUsuariosServlet extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
    response.setContentType("text/html;charset=UTF-8");
    try (PrintWriter out = response.getWriter()) {
      out.println("<!DOCTYPE html><html><head><title>Usuários</title></head><body>");
      out.println("<h2>Lista de Usuários</h2>");
      try {
        UsuarioDAO dao = new UsuarioDAO();
        List<Usuario> usuarios = dao.listarTodos();
        if (usuarios.isEmpty()) {
          out.println("<p>Nenhum usuário cadastrado.</p>");
        } else {
          out.println("<table><tr><th>ID</th><th>Nome</th><th>Email</th><th>Idade</th><th>Data</th></tr>");
          for (Usuario u : usuarios) {
            out.println("<tr><td>"+u.getId()+"</td><td>"+u.getNome()+"</td><td>"+u.getEmail()+"</td><td>"+u.getIdade()+"</td><td>"+u.getDataCadastro()+"</td></tr>");
          }
          out.println("</table>");
        }
      } catch (SQLException e) {
        out.println("<p style='color:red'>Erro: "+e.getMessage()+"</p>");
      }
      out.println("<a href='index.html'>Voltar</a></body></html>");
    }
  }
}
```

### Pontos-Chave
- Use `PreparedStatement` para segurança e legibilidade.
- Crie a tabela no construtor do DAO para garantir o esquema durante o desenvolvimento.
- Retorne objetos de domínio com um método `criarUsuario(ResultSet)` para centralizar o mapeamento.

---

## Exercício Prático 3: Sistema de Usuários (Cadastro e Busca) <a id="ex3"></a>

### Objetivo
Construir formulários HTML e Servlets para cadastrar e buscar usuários, validando dados.

### 1) Formulário de Cadastro
```html
<form action="CadastrarUsuarioServlet" method="post">
  <p><label>Nome:</label><input type="text" name="nome" required></p>
  <p><label>Email:</label><input type="email" name="email" required></p>
  <p><label>Idade:</label><input type="number" name="idade" min="1" max="120" required></p>
  <p><button type="submit">Cadastrar</button></p>
  <!-- Exiba mensagens baseadas em query string: sucesso/erro -->
</form>
```

### 2) Servlet de Cadastro
```java
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

### 3) Formulário de Busca
```html
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
  <!-- Exiba mensagens de erro conforme necessário -->
  </form>
```

### 4) Servlet de Busca (email e id implementados)
```java
@WebServlet(name = "BuscarUsuarioServlet", urlPatterns = {"/BuscarUsuarioServlet"})
public class BuscarUsuarioServlet extends HttpServlet {
  @Override
  protected void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
    String tipo = request.getParameter("tipo");
    String termo = request.getParameter("termo");
    response.setContentType("text/html;charset=UTF-8");
    try (PrintWriter out = response.getWriter()) {
      out.println("<!DOCTYPE html><html><head><title>Busca</title></head><body>");
      out.println("<h2>Resultado da Busca</h2>");
      try {
        UsuarioDAO dao = new UsuarioDAO();
        Usuario usuario = null;
        switch (tipo) {
          case "email": usuario = dao.buscarPorEmail(termo); break;
          case "id":
            try { usuario = dao.buscarPorId(Integer.parseInt(termo)); }
            catch (NumberFormatException nfe) {
              out.println("<p style='color:red'>ID inválido.</p><a href='buscar-usuario.html'>Voltar</a></body></html>");
              return;
            }
            break;
          case "nome":
            out.println("<p style='color:red'>Busca por nome não implementada.</p>");
            out.println("<a href='buscar-usuario.html'>Nova Busca</a> | <a href='index.html'>Início</a></body></html>");
            return;
        }
        if (usuario != null) {
          out.println("<p style='color:green'>Usuário encontrado!</p>");
          out.println("<table><tr><th>Campo</th><th>Valor</th></tr>" +
            "<tr><td>ID</td><td>"+usuario.getId()+"</td></tr>" +
            "<tr><td>Nome</td><td>"+usuario.getNome()+"</td></tr>" +
            "<tr><td>Email</td><td>"+usuario.getEmail()+"</td></tr>" +
            "<tr><td>Idade</td><td>"+usuario.getIdade()+"</td></tr>" +
            "<tr><td>Data Cadastro</td><td>"+usuario.getDataCadastro()+"</td></tr></table>");
        } else {
          out.println("<p style='color:red'>Usuário não encontrado.</p>");
        }
      } catch (SQLException e) {
        out.println("<p style='color:red'>Erro BD: "+e.getMessage()+"</p>");
      }
      out.println("<a href='buscar-usuario.html'>Nova Busca</a> | <a href='index.html'>Voltar</a></body></html>");
    }
  }
}
```
