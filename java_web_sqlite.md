# JAVA WEB E SQLITE - AULA PRÁTICA E GUIADA
## Módulo 3 - Integração Web + BD com NetBeans, GlassFish e SQLite

## Sumário

1. [Conteúdo Teórico](#conteúdo-teórico)
    - [Conceitos de JDBC com SQLite](#31-conceitos-de-jdbc-com-sqlite)
    - [Conexão com SQLite](#32-conexão-com-sqlite)
    - [CRUD Operations](#33-crud-operations)
    - [PreparedStatement](#34-preparedstatement)
    - [Transações e Foreign Keys](#35-transações-e-foreign-keys)
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

✅ Conectar aplicações Java Web com banco de dados SQLite (arquivo local ou memória)
✅ Implementar operações CRUD (Create, Read, Update, Delete)
✅ Usar PreparedStatement para consultas seguras
✅ Aplicar transações e habilitar chaves estrangeiras no SQLite
✅ Aplicar o padrão DAO (Data Access Object)
✅ Criar aplicações web funcionais com persistência leve, sem servidor de BD

---

## 📚 CONTEÚDO TEÓRICO

### 3.1 CONCEITOS DE JDBC COM SQLITE

**O que é JDBC?**
- JDBC é a API padrão do Java para acessar bancos de dados via SQL.
- O SQLite é um banco de dados leve, embutido, baseado em arquivo.

**Componentes Principais:**
- ✅ Driver SQLite: `org.sqlite.JDBC` (fornecido por `sqlite-jdbc` da Xerial)
- ✅ Connection: conexão com o arquivo (ex.: `C:/data/app.db`) ou memória
- ✅ PreparedStatement: execução segura de comandos SQL
- ✅ ResultSet: leitura de resultados

**Quando usar SQLite?**
- Projetos pequenos, protótipos, ambientes educacionais ou apps com baixa concorrência.
- Sem necessidade de instalar servidor; o BD é um arquivo.

---

### 3.2 CONEXÃO COM SQLITE

#### Dependência (Driver Xerial)
- Baixe `sqlite-jdbc-(versão).jar` em `https://github.com/xerial/sqlite-jdbc/releases`.
- Adicione ao projeto (NetBeans → Tools → Libraries ou Project Properties → Libraries).

#### URLs de Conexão
```java
// Arquivo no disco (cria se não existir)
String url = "jdbc:sqlite:C:/dados/aula_java_web.db";

// Caminho relativo (na pasta do projeto/servidor)
String urlRelativa = "jdbc:sqlite:./data/aula_java_web.db";

// Banco em memória (volátil, some ao encerrar)
String urlMemoria = "jdbc:sqlite::memory:";
```

#### Conexão Básica
```java
try {
    Class.forName("org.sqlite.JDBC");
    Connection conn = DriverManager.getConnection("jdbc:sqlite:./data/aula_java_web.db");
    // ... usar a conexão ...
    conn.close();
} catch (ClassNotFoundException | SQLException e) {
    e.printStackTrace();
}
```

#### Habilitar Foreign Keys
Por padrão, o SQLite exige ativação explícita de chaves estrangeiras por conexão:
```java
try (Statement st = conn.createStatement()) {
    st.execute("PRAGMA foreign_keys = ON");
}
```

---

### 3.3 CRUD OPERATIONS

#### Create (Inserir)
```java
String sql = "INSERT INTO usuarios (nome, email, idade) VALUES (?, ?, ?)";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setString(1, "João Silva");
    stmt.setString(2, "joao@email.com");
    stmt.setInt(3, 25);
    stmt.executeUpdate();
}
```

#### Read (Consultar)
```java
String sql = "SELECT id, nome, email, idade, data_cadastro FROM usuarios WHERE idade > ?";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setInt(1, 18);
    try (ResultSet rs = stmt.executeQuery()) {
        while (rs.next()) {
            int id = rs.getInt("id");
            String nome = rs.getString("nome");
            String email = rs.getString("email");
            int idade = rs.getInt("idade");
            String data = rs.getString("data_cadastro");
        }
    }
}
```

#### Update (Atualizar)
```java
String sql = "UPDATE usuarios SET idade = ? WHERE id = ?";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setInt(1, 26);
    stmt.setInt(2, 1);
    stmt.executeUpdate();
}
```

#### Delete (Excluir)
```java
String sql = "DELETE FROM usuarios WHERE id = ?";
try (PreparedStatement stmt = conn.prepareStatement(sql)) {
    stmt.setInt(1, 1);
    stmt.executeUpdate();
}
```

Observações do SQLite:
- Use `INTEGER PRIMARY KEY` para chaves auto-incrementadas (sinônimo da coluna `rowid`).
- `BOOLEAN` costuma ser representado como `INTEGER` (0/1) ou `TEXT`.
- Tipagem é dinâmica (affinity). Valide dados no app.

---

### 3.4 PREPAREDSTATEMENT

✅ Segurança contra SQL Injection
✅ Reuso e legibilidade

```java
// Vulnerável (NÃO USE)
String sql = "SELECT * FROM usuarios WHERE nome = '" + nome + "'";

// Seguro
String sql = "SELECT * FROM usuarios WHERE nome = ?";
try (PreparedStatement ps = conn.prepareStatement(sql)) {
    ps.setString(1, nome);
}
```

---

### 3.5 TRANSAÇÕES E FOREIGN KEYS

```java
Connection conn = DriverManager.getConnection("jdbc:sqlite:./data/aula_java_web.db");
try {
    conn.setAutoCommit(false);
    try (Statement st = conn.createStatement()) {
        st.execute("PRAGMA foreign_keys = ON");
    }
    // ... várias operações SQL ...
    conn.commit();
} catch (SQLException e) {
    if (conn != null) conn.rollback();
    throw e;
} finally {
    if (conn != null) conn.setAutoCommit(true);
    conn.close();
}
```

---

### 3.6 PADRÃO DAO

```java
public interface UsuarioDAO {
    void inserir(Usuario usuario) throws SQLException;
    Usuario buscarPorId(int id) throws SQLException;
    Usuario buscarPorEmail(String email) throws SQLException;
    List<Usuario> listarTodos() throws SQLException;
    void atualizar(Usuario usuario) throws SQLException;
    void excluir(int id) throws SQLException;
}
```

---

## 🛠️ CONTEÚDO PRÁTICO

### CONFIGURAÇÃO DO AMBIENTE

1) Adicionar driver SQLite JDBC (Xerial) ao projeto
2) Criar pasta `data/` para armazenar o arquivo `aula_java_web.db`
3) Verificar GlassFish: o SQLite não é ideal para pool; use `DriverManager` simples

#### SQL de Criação de Tabela (executar no primeiro uso)
```sql
CREATE TABLE IF NOT EXISTS usuarios (
    id INTEGER PRIMARY KEY,
    nome TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    idade INTEGER,
    data_cadastro TEXT DEFAULT (datetime('now'))
);

-- Dados de teste
INSERT INTO usuarios (nome, email, idade) VALUES
('João Silva', 'joao@email.com', 25),
('Maria Santos', 'maria@email.com', 30),
('Pedro Oliveira', 'pedro@email.com', 22);
```

### EXERCÍCIO PRÁTICO 1: CONEXÃO BÁSICA

#### Servlet de Teste de Conexão
```java
@WebServlet(name = "TesteConexaoSQLiteServlet", urlPatterns = {"/teste-conexao-sqlite"})
public class TesteConexaoSQLiteServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html><html><head><title>Teste SQLite</title>");
            out.println("<style>body{font-family:Arial;margin:40px}.ok{color:green;background:#e8f5e8;padding:10px;border-radius:5px}.err{color:red;background:#ffe8e8;padding:10px;border-radius:5px}</style>");
            out.println("</head><body><h2>Teste de Conexão com SQLite</h2>");
            try {
                Class.forName("org.sqlite.JDBC");
                String url = getServletContext().getRealPath("/WEB-INF/data/aula_java_web.db");
                String jdbc = "jdbc:sqlite:" + url;
                try (Connection conn = DriverManager.getConnection(jdbc)) {
                    try (Statement st = conn.createStatement()) {
                        st.execute("PRAGMA foreign_keys = ON");
                    }
                    out.println("<div class='ok'><strong>Conexão OK:</strong> " + jdbc + "</div>");
                    try (Statement st = conn.createStatement()) {
                        st.execute("CREATE TABLE IF NOT EXISTS usuarios (id INTEGER PRIMARY KEY, nome TEXT NOT NULL, email TEXT UNIQUE NOT NULL, idade INTEGER, data_cadastro TEXT DEFAULT (datetime('now')))");
                    }
                }
            } catch (ClassNotFoundException | SQLException e) {
                out.println("<div class='err'><strong>Erro:</strong> " + e.getMessage() + "</div>");
            }
            out.println("<br><a href='index.html'>Voltar</a></body></html>");
        }
    }
}
```

### EXERCÍCIO PRÁTICO 2: CRUD COMPLETO

#### Model `Usuario`
```java
public class Usuario {
    private int id;
    private String nome;
    private String email;
    private int idade;
    private String dataCadastro; // ISO-8601 em TEXT
    // getters/setters/toString
}
```

#### DAO com SQLite
```java
public class UsuarioSQLiteDAO implements UsuarioDAO {
    private final String jdbcUrl;

    public UsuarioSQLiteDAO(String jdbcUrl) { this.jdbcUrl = jdbcUrl; }

    private Connection obterConexao() throws SQLException { return DriverManager.getConnection(jdbcUrl); }

    @Override
    public void inserir(Usuario u) throws SQLException {
        String sql = "INSERT INTO usuarios (nome, email, idade) VALUES (?, ?, ?)";
        try (Connection c = obterConexao(); PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setString(1, u.getNome());
            ps.setString(2, u.getEmail());
            ps.setInt(3, u.getIdade());
            ps.executeUpdate();
        }
    }

    @Override
    public Usuario buscarPorId(int id) throws SQLException {
        String sql = "SELECT * FROM usuarios WHERE id = ?";
        try (Connection c = obterConexao(); PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setInt(1, id);
            try (ResultSet rs = ps.executeQuery()) {
                if (rs.next()) return map(rs);
            }
        }
        return null;
    }

    @Override
    public Usuario buscarPorEmail(String email) throws SQLException {
        String sql = "SELECT * FROM usuarios WHERE email = ?";
        try (Connection c = obterConexao(); PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setString(1, email);
            try (ResultSet rs = ps.executeQuery()) {
                if (rs.next()) return map(rs);
            }
        }
        return null;
    }

    @Override
    public List<Usuario> listarTodos() throws SQLException {
        String sql = "SELECT * FROM usuarios ORDER BY nome";
        List<Usuario> lista = new ArrayList<>();
        try (Connection c = obterConexao(); PreparedStatement ps = c.prepareStatement(sql); ResultSet rs = ps.executeQuery()) {
            while (rs.next()) lista.add(map(rs));
        }
        return lista;
    }

    @Override
    public void atualizar(Usuario u) throws SQLException {
        String sql = "UPDATE usuarios SET nome = ?, email = ?, idade = ? WHERE id = ?";
        try (Connection c = obterConexao(); PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setString(1, u.getNome());
            ps.setString(2, u.getEmail());
            ps.setInt(3, u.getIdade());
            ps.setInt(4, u.getId());
            ps.executeUpdate();
        }
    }

    @Override
    public void excluir(int id) throws SQLException {
        String sql = "DELETE FROM usuarios WHERE id = ?";
        try (Connection c = obterConexao(); PreparedStatement ps = c.prepareStatement(sql)) {
            ps.setInt(1, id);
            ps.executeUpdate();
        }
    }

    private Usuario map(ResultSet rs) throws SQLException {
        Usuario u = new Usuario();
        u.setId(rs.getInt("id"));
        u.setNome(rs.getString("nome"));
        u.setEmail(rs.getString("email"));
        u.setIdade(rs.getInt("idade"));
        u.setDataCadastro(rs.getString("data_cadastro"));
        return u;
    }
}
```

#### Servlet para Listar Usuários
```java
@WebServlet(name = "ListarUsuariosSQLiteServlet", urlPatterns = {"/listar-usuarios-sqlite"})
public class ListarUsuariosSQLiteServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        response.setContentType("text/html;charset=UTF-8");
        try (PrintWriter out = response.getWriter()) {
            out.println("<!DOCTYPE html><html><head><title>Usuários (SQLite)</title>");
            out.println("<style>body{font-family:Arial;margin:40px}table{border-collapse:collapse;width:100%}th,td{border:1px solid #ddd;padding:8px}th{background:#f2f2f2}</style>");
            out.println("</head><body><h2>Lista de Usuários (SQLite)</h2>");
            String path = getServletContext().getRealPath("/WEB-INF/data/aula_java_web.db");
            String jdbc = "jdbc:sqlite:" + path;
            UsuarioSQLiteDAO dao = new UsuarioSQLiteDAO(jdbc);
            try {
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
                out.println("<div style='color:red'>Erro: "+e.getMessage()+"</div>");
            }
            out.println("<br><a href='index.html'>Voltar</a></body></html>");
        }
    }
}
```

### EXERCÍCIO PRÁTICO 3: SISTEMA DE USUÁRIOS

#### Formulário de Cadastro (HTML simplificado)
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Cadastrar Usuário (SQLite)</title>
  <style>
    body{font-family:Arial;margin:40px}form{background:#f9f9f9;padding:20px;border-radius:5px;max-width:400px}
    input{width:100%;padding:8px;margin:6px 0;border:1px solid #ddd;border-radius:3px}
    button{background:#3ecf8e;color:#fff;border:0;padding:10px 16px;border-radius:4px;cursor:pointer}
  </style>
<\/head>
<body>
  <h2>Cadastrar Novo Usuário</h2>
  <form action="CadastrarUsuarioSQLiteServlet" method="post">
    <label>Nome</label>
    <input type="text" name="nome" required>
    <label>Email</label>
    <input type="email" name="email" required>
    <label>Idade</label>
    <input type="number" name="idade" min="1" max="120" required>
    <button type="submit">Cadastrar</button>
  </form>
  <br><a href="index.html">Voltar</a>
</body>
</html>
```

#### Servlet de Cadastro
```java
@WebServlet(name = "CadastrarUsuarioSQLiteServlet", urlPatterns = {"/CadastrarUsuarioSQLiteServlet"})
public class CadastrarUsuarioSQLiteServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        String nome = request.getParameter("nome");
        String email = request.getParameter("email");
        int idade = Integer.parseInt(request.getParameter("idade"));

        String path = getServletContext().getRealPath("/WEB-INF/data/aula_java_web.db");
        String jdbc = "jdbc:sqlite:" + path;
        UsuarioSQLiteDAO dao = new UsuarioSQLiteDAO(jdbc);
        try {
            if (dao.buscarPorEmail(email) != null) {
                response.sendRedirect("formulario-usuario.html?erro=email_existe");
                return;
            }
            Usuario u = new Usuario();
            u.setNome(nome); u.setEmail(email); u.setIdade(idade);
            dao.inserir(u);
            response.sendRedirect("listar-usuarios-sqlite?sucesso=cadastrado");
        } catch (SQLException e) {
            response.sendRedirect("formulario-usuario.html?erro=banco_dados");
        }
    }
}
```

---

## 📋 EXERCÍCIOS DE FIXAÇÃO

### Exercício 1: Produtos (SQLite)
- Tabela `produtos` (id INTEGER PK, nome TEXT, preco REAL, categoria TEXT, estoque INTEGER)
- CRUD completo + listagem com filtro por categoria

### Exercício 2: Vendas (SQLite)
- Tabela `vendas` (id INTEGER PK, cliente TEXT, produto_id INTEGER, quantidade INTEGER, valor_total REAL, data TEXT)
- Habilitar `PRAGMA foreign_keys = ON` e FK para `produtos(id)`
- Relatório por período

### Exercício 3: Login Básico (SQLite)
- Tabela `usuarios_auth` (id INTEGER PK, login TEXT UNIQUE, senha_hash TEXT, perfil TEXT)
- Tela de login, validação e sessão

---

## 🔧 DICAS IMPORTANTES

- SQLite é embutido; não há servidor nem usuário/senha por padrão.
- ConcorRência: ideal para baixa escrita concorrente. Em alta concorrência, considere um SGBD servidor.
- Caminho do arquivo: use diretórios de escrita do servidor (ex.: `WEB-INF/data`).
- Pool de conexões: geralmente desnecessário; conexões são leves. Prefira `try-with-resources`.
- Tipos: use `INTEGER`, `REAL`, `TEXT`, `BLOB`. Datas como `TEXT` ISO (`datetime('now')`).
- Ative `PRAGMA foreign_keys = ON` por conexão ou logo após abrir a conexão.

---

## 📚 RESUMO DOS CONCEITOS

| Conceito | Descrição | Exemplo |
|---------|-----------|---------|
| JDBC SQLite | Acesso via driver Xerial | `org.sqlite.JDBC` |
| URL | Caminho do arquivo ou memória | `jdbc:sqlite:./data/app.db` |
| CRUD | Operações básicas | `INSERT/SELECT/UPDATE/DELETE` |
| DAO | Acesso a dados desacoplado | `UsuarioSQLiteDAO` |
| PRAGMA | Configurações do SQLite | `foreign_keys = ON` |

---

## 🎯 CHECKLIST DE APRENDIZAGEM

### Conceitos Teóricos
- [ ] Entendi como o SQLite funciona como BD embutido
- [ ] Sei configurar a URL JDBC do SQLite
- [ ] Sei ativar `PRAGMA foreign_keys = ON`

### Implementação
- [ ] Configurei o driver Xerial e criei o arquivo `.db`
- [ ] Implementei CRUD com `PreparedStatement`
- [ ] Organizei o acesso a dados com DAO

### Boas Práticas
- [ ] Usei `try-with-resources` e fechei recursos
- [ ] Validei dados e tratei exceções
- [ ] Usei `WEB-INF/data` para armazenamento no servidor

---

**🎉 Pronto! Agora você consegue integrar Java Web com SQLite de forma simples e eficiente.**


