## Módulo 3: Java Server Pages (JSP) – Parte 1

JSP é uma tecnologia que permite misturar HTML estático com código Java dinâmico. O servidor de aplicação (WildFly) "traduz" o arquivo JSP para um Servlet Java, compila e executa, gerando o HTML final que é enviado ao navegador do cliente.

### 4.1 Scriptlets: Código Java Embutido

- **Teoria**: Scriptlets são blocos de código Java inseridos diretamente em uma página JSP, delimitados por `<%` e `%>`. Todo o código dentro de um scriptlet é executado no servidor a cada requisição. Embora poderosos, o uso excessivo pode deixar o código confuso e difícil de manter.

- **Prática Guiada**: Vamos criar uma página que usa um laço `for` para imprimir uma mensagem 5 vezes.

Antes de começar, vamos criar nosso projeto.

- **Novo Projeto**: Vá em Arquivo > Novo Projeto....
- **Categoria e Projeto**: Escolha Java Web na categoria e Aplicação Web no projeto. Clique em Próximo.
- **Nome e Local**: Dê um nome ao projeto, por exemplo, `AulaJSP`. Clique em Próximo.
- **Servidor e Configurações**:
  - No campo Servidor, selecione GlassFish Server. Se ele não estiver na lista, clique em Adicionar... e aponte para a pasta onde você descompactou o GlassFish.
  - Deixe a Versão do Java EE como Java EE 7 Web.
- **Finalize**: Clique em Finalizar.

O NetBeans criará um projeto com uma estrutura básica, incluindo uma página `index.html`. Vamos usar essa estrutura para criar nossos arquivos JSP.

  1. No seu projeto `AulaJSP`, clique com o botão direito sobre `Páginas Web`.
  2. Vá em Novo > Página JSP....
  3. Dê o nome de `scriptlet_exemplo.jsp` e clique em Finalizar.
  4. Substitua o conteúdo do arquivo pelo código abaixo:

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de Scriptlet</title>
    </head>
    <body>
        <h1>Usando Scriptlets para criar uma lista!</h1>
        <ul>
            <%
                // Este é um scriptlet. Código Java puro!
                for (int i = 1; i <= 5; i++) {
                    // Note como misturamos HTML dentro do laço Java
                    out.println("<li>Item número " + i + "</li>");
                }
            %>
        </ul>
    </body>
</html>
```

- **Para executar**: Clique com o botão direito no arquivo `scriptlet_exemplo.jsp` e selecione Executar Arquivo. O NetBeans iniciará o GlassFish (se necessário) e abrirá a página no navegador. Você verá uma lista com 5 itens.

- **Exercícios de Múltipla Escolha (4.1)**:
  - Qual é a sintaxe correta para iniciar e terminar um scriptlet JSP?
    - a) `<?` e `?>`
    - b) `<%` e `%>`
    - c) `<jsp:` e `</jsp>`
    - d) `<!--` e `-->`

<details>
<summary>Resposta</summary>
<p>b) `<%` e `%>`</p>
</details>

---

### 4.2 Declarações: Definição de Variáveis e Métodos

- **Teoria**: Declarações JSP, delimitadas por `<%! ... %>`, são usadas para declarar variáveis e métodos que se tornam parte da classe do Servlet gerado. Diferem das variáveis em scriptlets (locais ao método `service`), pois viram membros da classe.

- **Prática Guiada**: Vamos criar um contador de visitas simples que usa uma declaração para manter o número.
  1. Crie um novo arquivo chamado `declaracao_exemplo.jsp`.
  2. Insira o código:

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de Declaração</title>
    </head>
    <body>
        <h1>Contador de Acessos Simples</h1>
        
        <%! 
            // DECLARAÇÃO: Esta variável é um membro da classe do servlet.
            // Ela mantém seu valor entre as requisições.
            private int contador = 0;

            // DECLARAÇÃO: Este método também é um membro da classe.
            public String getMensagem() {
                return "Bem-vindo de volta!";
            }
        %>
        
        <%
            // SCRIPTLET: Este código é executado a cada requisição.
            contador++; 
        %>
        
        <p>Esta página foi acessada <%= contador %> vezes.</p>
        <p><%= getMensagem() %></p>
        
    </body>
</html>
```

- **Execute** e atualize a página (F5) várias vezes; observe o contador aumentar.

- **Exercícios de Múltipla Escolha (4.2)**:
  - Uma variável declarada dentro de `<%! int x = 10; %>` é:
    - a) Uma variável local, reiniciada a cada requisição.
    - b) Uma variável de instância do Servlet, que mantém seu valor entre requisições de diferentes usuários.
    - c) Uma constante que não pode ser alterada.
    - d) Uma variável global para toda a aplicação web.

<details>
<summary>Resposta</summary>
<p>b) Uma variável de instância do Servlet, que mantém seu valor entre requisições de diferentes usuários.</p>
</details>

---

### 4.3 Expressões: Exibição de Valores

- **Teoria**: Expressões JSP, com a sintaxe `<%= ... %>`, imprimem o valor de uma variável ou expressão Java diretamente no HTML. O conteúdo é avaliado, convertido em `String` e inserido na página; é um atalho para `out.print(...)`.

- **Prática Guiada**:
  1. Crie um novo arquivo `expressao_exemplo.jsp`.
  2. Insira o código:

```jsp
<%@page import="java.util.Date"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de Expressão</title>
    </head>
    <body>
        <h1>Usando Expressões JSP</h1>
        
        <p>A data e hora atuais no servidor são: <strong><%= new Date() %></strong></p>
        
        <p>O resultado de 15 * 4 é: <strong><%= 15 * 4 %></strong></p>
        
        <%
            String nomeUsuario = "Aluno Técnico";
        %>
        <p>Bem-vindo, <strong><%= nomeUsuario.toUpperCase() %></strong>!</p>
        
    </body>
</html>
```

- **Execute** o arquivo para ver os valores e a data/hora.

- **Exercícios de Múltipla Escolha (4.3)**:
  - O código `<%= "Olá, Mundo!" %>` é equivalente a qual código dentro de um scriptlet?
    - a) `System.out.println("Olá, Mundo!");`
    - b) `return "Olá, Mundo!";`
    - c) `out.print("Olá, Mundo!");`
    - d) `page.print("Olá, Mundo!");`

<details>
<summary>Resposta</summary>
<p>c) `out.print("Olá, Mundo!");`</p>
</details>

---

### 4.4 Objetos Implícitos

- **Teoria**: O container JSP disponibiliza automaticamente um conjunto de objetos Java em todas as páginas ("objetos implícitos"). Principais:
  - `request`: requisição HTTP do cliente (parâmetros, cabeçalhos, etc.).
  - `response`: resposta HTTP enviada ao cliente.
  - `session`: sessão do usuário; guarda informações entre requisições do mesmo usuário.
  - `application`: aplicação web como um todo; dados compartilhados por todos os usuários.

- **Prática Guiada**:
  1. Crie o arquivo `objetos_implicitos.jsp`.
  2. Insira o código:

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Exemplo de Objetos Implícitos</title>
    </head>
    <body>
        <h1>Explorando Objetos Implícitos</h1>
        
        <h3>Objeto 'request'</h3>
        <p>Seu endereço IP é: <%= request.getRemoteAddr() %></p>
        <p>O método da requisição foi: <%= request.getMethod() %></p>
        
        <hr>
        
        <h3>Objeto 'session'</h3>
        <%
            // Pega o contador da sessão
            Integer contadorSessao = (Integer) session.getAttribute("meuContador");
            
            if (contadorSessao == null) {
                // Se for a primeira vez, inicializa com 1
                contadorSessao = 1;
            } else {
                // Se não, incrementa
                contadorSessao++;
            }
            
            // Guarda o novo valor na sessão
            session.setAttribute("meuContador", contadorSessao);
        %>
        
        <p>Você (nesta sessão) visitou esta página <strong><%= contadorSessao %></strong> vezes.</p>
        <p><em>Abra esta página em outro navegador (ou aba anônima) e veja que o contador será diferente!</em></p>
        
    </body>
</html>
```

- **Execute** e compare em aba anônima para ver a diferença de sessão.

- **Exercícios de Múltipla Escolha (4.4)**:
  - Qual objeto implícito deve ser usado para armazenar informações que persistam durante várias visitas de um único usuário ao site?
    - a) `request`
    - b) `application`
    - c) `response`
    - d) `session`

<details>
<summary>Resposta</summary>
<p>d) `session`</p>
</details>

---

## Módulo 4: Java Server Pages (JSP) – Parte 2

Agora que conhecemos o básico, vamos avançar para técnicas que tornam o JSP mais organizado, reutilizável e poderoso, afastando-nos do uso excessivo de scriptlets.

### 5.1 Diretivas

- **Teoria**: Diretivas são mensagens para o container JSP que afetam o processamento da página inteira (processadas na tradução para Servlet). Sintaxe: `<%@ ... %>`.
  - `page`: define atributos da página (`import`, `contentType`, `errorPage`, etc.).
  - `include`: inclui o conteúdo de outro arquivo durante a fase de tradução (inclusão estática). Útil para cabeçalhos e rodapés.
  - `taglib`: declara uma biblioteca de tags (essencial para usar JSTL).

- **Prática Guiada**:
  1. Crie `cabecalho.jsp`:

```jsp
<%-- /Páginas Web/cabecalho.jsp --%>
<div style="background-color: #f0f0f0; padding: 10px; border-bottom: 1px solid #ccc;">
    <h1>Meu Site Dinâmico</h1>
    <p>Bem-vindo ao nosso curso de JSP!</p>
</div>
<hr>
```

  2. Crie `rodape.jsp`:

```jsp
<%-- /Páginas Web/rodape.jsp --%>
<hr>
<div style="text-align: center; font-size: 12px; color: #666;">
    <p>&copy; 2023 - Escola Técnica XYZ. Todos os direitos reservados.</p>
</div>
```

  3. Crie a página principal `diretiva_include.jsp` e use `include` e `page`:

```jsp
<%@page import="java.util.ArrayList, java.util.List"%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Diretivas JSP</title>
    </head>
    <body>
        
        <%@ include file="cabecalho.jsp" %>
        
        <h2>Conteúdo Principal da Página</h2>
        <p>Esta página demonstra o uso da diretiva `page` para importar classes e da diretiva `include` para reutilizar componentes.</p>
        
        <%
            // Graças ao <%@page import...%> podemos usar ArrayList sem o caminho completo
            List<String> frutas = new ArrayList<>();
            frutas.add("Maçã");
            frutas.add("Banana");
            frutas.add("Laranja");
        %>
        
        <p>Lista de Frutas: <%= frutas.toString() %></p>
        
        <%@ include file="rodape.jsp" %>
        
    </body>
</html>
```

- **Execute** `diretiva_include.jsp` para ver cabeçalho, conteúdo e rodapé.

- **Exercícios de Múltipla Escolha (5.1)**:
  - Qual diretiva é usada para disponibilizar uma biblioteca de tags, como a JSTL, em uma página JSP?
    - a) `<%@ page ... %>`
    - b) `<%@ include ... %>`
    - c) `<%@ taglib ... %>`
    - d) `<%@ library ... %>`

<details>
<summary>Resposta</summary>
<p>c) `<%@ taglib ... %>`</p>
</details>

---

### 5.2 Ações

- **Teoria**: Ações JSP são tags XML (`<jsp:nomeDaAcao>`) que executam tarefas no servidor durante a requisição. São comuns na interação com JavaBeans.
  - Um JavaBean:
    - é público;
    - tem construtor público sem argumentos;
    - tem atributos privados;
    - expõe getters e setters públicos.
  - Ações importantes:
    - `<jsp:useBean>`: localiza ou cria uma instância de um JavaBean.
    - `<jsp:setProperty>`: define valor de propriedade do bean (pode mapear parâmetros da requisição automaticamente).
    - `<jsp:getProperty>`: obtém e exibe o valor de uma propriedade do bean.

- **Prática Guiada**: Vamos criar um formulário HTML, uma classe `Aluno` (JavaBean) e uma JSP para processar os dados usando ações.

  1. **Crie o JavaBean** (`br.com.escola.modelo.Aluno`):

```java
package br.com.escola.modelo;

public class Aluno implements java.io.Serializable {
    private String nome;
    private int matricula;

    // Construtor padrão obrigatório
    public Aluno() {
    }

    // Getters e Setters
    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public int getMatricula() {
        return matricula;
    }

    public void setMatricula(int matricula) {
        this.matricula = matricula;
    }
}
```

  2. **Crie o formulário** (`form_aluno.html`):

```html
<!DOCTYPE html>
<html>
<head>
    <title>Cadastro de Aluno</title>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Formulário de Cadastro de Aluno</h1>
    <form action="processa_aluno.jsp" method="POST">
        Nome: <input type="text" name="nome" /><br/><br/>
        Matrícula: <input type="text" name="matricula" /><br/><br/>
        <input type="submit" value="Enviar Dados" />
    </form>
</body>
</html>
```

  3. **Crie a página de processamento** (`processa_aluno.jsp`):

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Dados do Aluno</title>
    </head>
    <body>
        <h1>Dados Recebidos</h1>

        <%-- 1. Cria ou localiza uma instância da classe Aluno e a chama de "alunoBean" --%>
        <jsp:useBean id="alunoBean" class="br.com.escola.modelo.Aluno" scope="request" />

        <%-- 2. Pega TODOS os parâmetros da requisição (nome, matricula)
               e chama os setters correspondentes no alunoBean (setNome, setMatricula) --%>
        <jsp:setProperty name="alunoBean" property="*" />

        <h3>Dados do Aluno:</h3>
        <p>
            Nome: <jsp:getProperty name="alunoBean" property="nome" /><br/>
            Matrícula: <jsp:getProperty name="alunoBean" property="matricula" />
        </p>

        <p><em>(Isso foi feito sem uma única linha de scriptlet!)</em></p>

    </body>
</html>
```

- **Execute** `form_aluno.html`. Preencha e envie; `processa_aluno.jsp` exibirá os dados populados automaticamente no JavaBean.

- **Exercícios de Múltipla Escolha (5.2)**:
  - O que a ação `<jsp:setProperty name="meuBean" property="*"/>` faz?
    - a) Define a propriedade chamada `*` do bean "meuBean".
    - b) Mapeia automaticamente todos os parâmetros da requisição HTTP cujos nomes coincidam com as propriedades do bean "meuBean".
    - c) Cria uma cópia de todas as propriedades do bean "meuBean".
    - d) Define todas as propriedades do bean "meuBean" como nulas.

<details>
<summary>Resposta</summary>
<p>b) Mapeia automaticamente todos os parâmetros da requisição HTTP cujos nomes coincidam com as propriedades do bean "meuBean".</p>
</details>

---

### 5.3 Linguagem de Expressão – EL (Expression Language)

- **Teoria**: A EL facilita o acesso a dados (principalmente JavaBeans e atributos de escopo) sem scriptlets ou `<jsp:getProperty>`. Sintaxe: `${expressao}`. Pode acessar:
  - Propriedades de JavaBeans: `${alunoBean.nome}`
  - Atributos de escopo: `${sessionScope.usuarioLogado}`
  - Valores de mapas, arrays e listas
  - Operações matemáticas e lógicas: `${(10 * 5) + 2}` ou `${carrinho.total > 100}`

- **Prática Guiada**: Refaça `processa_aluno.jsp` usando EL.
  1. Crie uma cópia como `processa_aluno_el.jsp`.
  2. Modifique a exibição dos dados:

```jsp
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <title>Dados do Aluno com EL</title>
    </head>
    <body>
        <h1>Dados Recebidos (com Expression Language)</h1>

        <jsp:useBean id="alunoBean" class="br.com.escola.modelo.Aluno" scope="request" />
        <jsp:setProperty name="alunoBean" property="*" />

        <h3>Dados do Aluno (acessados com EL):</h3>
        <p>
            <%-- A EL acessa diretamente a propriedade via getNome() --%>
            Nome: ${alunoBean.nome}<br/>
            Matrícula: ${alunoBean.matricula}
        </p>
        
        <p><strong>Nome em maiúsculas:</strong> ${alunoBean.nome.toUpperCase()}</p>
        
        <p><em>Código muito mais limpo e legível!</em></p>
    </body>
</html>
```

- **Altere** a `action` do `form_aluno.html` para `processa_aluno_el.jsp` e teste novamente.

- **Exercícios de Múltipla Escolha (5.3)**:
  - Se um objeto `Produto` com um método `getPreco()` está armazenado na sessão com o nome `prod`, qual é a sintaxe correta da EL para exibir seu preço?
    - a) `<%= session.getAttribute("prod").getPreco() %>`
    - b) `${sessionScope.prod.getPreco()}`
    - c) `${session.prod.preco}`
    - d) `${sessionScope.prod.preco}`

<details>
<summary>Resposta</summary>
<p>d) `${sessionScope.prod.preco}` (A EL chama o `getPreco()` automaticamente ao acessar a propriedade `preco`)</p>
</details>

---

### 5.4 Biblioteca de Tags Padrão – JSTL (JavaServer Pages Standard Tag Library)

- **Teoria**: JSTL permite eliminar quase todo o código Java das JSPs com tags para laços, condicionais, formatação, etc.
  - Para usar:
    - Adicione a dependência JSTL ao projeto.
    - Declare a biblioteca na JSP com `taglib`.
  - Biblioteca Core (exemplos):
    - `<c:out value="${...}" />` – exibir valores (proteção contra XSS).
    - `<c:set var="nome" value="${...}" />` – criar variável.
    - `<c:if test="${...}"> ... </c:if>` – condicional.
    - `<c:forEach var="item" items="${lista}"> ... </c:forEach>` – iterar coleções.

- **Prática Guiada**: Criar página que exibe lista de alunos usando JSTL.

  1. **Adicionar a dependência JSTL** (Maven): abra `pom.xml` e, dentro de `<dependencies>`, adicione:

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

  2. **Crie** `lista_alunos_jstl.jsp`:

```jsp
<%@page import="java.util.ArrayList"%>
<%@page import="br.com.escola.modelo.Aluno"%>
<%@page import="java.util.List"%>

<%-- 1. Importa a biblioteca JSTL Core com o prefixo 'c' --%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>

<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <title>Lista de Alunos com JSTL</title>
        <style> table, th, td { border: 1px solid black; border-collapse: collapse; padding: 5px; } </style>
    </head>
    <body>
        <h1>Usando JSTL para Listar Alunos</h1>

        <%
            // Em uma aplicação real, esta lista viria do banco de dados.
            // Aqui, vamos criá-la manualmente para o exemplo.
            List<Aluno> listaDeAlunos = new ArrayList<>();
            
            Aluno a1 = new Aluno();
            a1.setNome("João da Silva");
            a1.setMatricula(101);
            
            Aluno a2 = new Aluno();
            a2.setNome("Maria Oliveira");
            a2.setMatricula(102);
            
            Aluno a3 = new Aluno();
            a3.setNome("Pedro Santos");
            a3.setMatricula(103);
            
            listaDeAlunos.add(a1);
            listaDeAlunos.add(a2);
            listaDeAlunos.add(a3);
            
            // Coloca a lista no escopo da requisição para a JSTL acessar
            request.setAttribute("alunos", listaDeAlunos);
        %>

        <c:if test="${not empty alunos}">
            <table>
                <tr>
                    <th>Matrícula</th>
                    <th>Nome</th>
                </tr>
                
                <%-- 2. Itera sobre a lista de alunos --%>
                <c:forEach var="aluno" items="${alunos}">
                    <tr>
                        <td><c:out value="${aluno.matricula}" /></td>
                        <td><c:out value="${aluno.nome}" /></td>
                    </tr>
                </c:forEach>
            </table>
        </c:if>

        <c:if test="${empty alunos}">
            <p>Não há alunos cadastrados.</p>
        </c:if>

    </body>
</html>
```

- **Execute** `lista_alunos_jstl.jsp` para ver a tabela gerada a partir da lista de objetos Java, sem `out.print` em scriptlets.

- **Exercícios de Múltipla Escolha (5.4)**:
  - Qual tag JSTL é usada para iterar sobre uma coleção de dados, como uma `ArrayList`?
    - a) `<c:if>`
    - b) `<c:loop>`
    - c) `<c:forEach>`
    - d) `<c:iterate>`

<details>
<summary>Resposta</summary>
<p>c) `<c:forEach>`</p>
</details>

---

## Conclusão

Neste material, você aprendeu a jornada completa do desenvolvimento com JSP:

- **Básico**: Scriptlets, Declarações e Expressões.
- **Estrutura**: Ações JSP com JavaBeans.
- **Modernização**: Expression Language (EL) para código limpo e legível.
- **Padrão atual**: JSTL para lógica de apresentação (laços, condicionais) de forma declarativa e sem Java na página.

A prática recomendada hoje é minimizar ou eliminar completamente o uso de scriptlets, preferindo a combinação de Servlets (para lógica de negócio), EL e JSTL (para exibir os dados na JSP).
