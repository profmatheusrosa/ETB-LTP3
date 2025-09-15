# EXPLICAÇÃO DETALHADA DAS PRÁTICAS JSP (Módulo 3)

## Índice

1. [Visão Geral do JSP](#visao-geral)
2. [4.1 Scriptlets: Código Java Embutido](#41-scriptlets)
3. [4.2 Declarações: Variáveis e Métodos](#42-declaracoes)
4. [4.3 Expressões: Exibição de Valores](#43-expressoes)
5. [4.4 Objetos Implícitos](#44-objetos-implicitos)
6. [5.1 Diretivas (`page`, `include`, `taglib`)](#51-diretivas)
7. [5.2 Ações JSP com JavaBeans](#52-acoes)
8. [5.3 Expression Language (EL)](#53-el)
9. [5.4 JSTL – Biblioteca de Tags Padrão](#54-jstl)
10. [Boas Práticas e Dicas](#boas-praticas)

---

## Visão Geral do JSP

O JSP permite misturar HTML com código Java para gerar conteúdo dinâmico. Cada página `.jsp` é traduzida para um Servlet pelo container (ex.: GlassFish/WildFly), compilada e executada, produzindo HTML para o navegador.

- Quando usar: renderização de páginas (camada de visão), com lógica mínima.
- Evitar: regras de negócio em JSP. Prefira Servlets, serviços e DAOs; na JSP, use EL e JSTL para exibir dados.

---

## 4.1 Scriptlets: Código Java Embutido <a id="41-scriptlets"></a>

### Código de Referência
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

### Explicação Linha a Linha (pontos-chave)
- `<%@page ... %>`: diretiva que configura a página (tipo de conteúdo e charset).
- `<% ... %>`: bloco de scriptlet; o código dentro roda no método `service` do Servlet gerado a cada requisição.
- `out.println(...)`: escreve diretamente no buffer de resposta da JSP.
- Laço `for`: controla a geração repetitiva de elementos HTML.

### Observações Importantes
- Scriptlets funcionam, mas dificultam manutenção. Prefira EL/JSTL para lógica de apresentação.
- Misturar muitos `out.println` com HTML reduz legibilidade.

---

## 4.2 Declarações: Variáveis e Métodos <a id="42-declaracoes"></a>

### Código de Referência
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
            // DECLARAÇÃO: membros da classe do Servlet gerado
            private int contador = 0;

            public String getMensagem() {
                return "Bem-vindo de volta!";
            }
        %>
        
        <%
            // SCRIPTLET: executa por requisição
            contador++; 
        %>
        
        <p>Esta página foi acessada <%= contador %> vezes.</p>
        <p><%= getMensagem() %></p>
        
    </body>
</html>
```

### Explicação
- `<%! ... %>`: bloco de declaração; seu conteúdo vira membro da classe do Servlet gerado (mantém estado entre requisições da mesma instância/threads, com cuidados de concorrência).
- `contador++` em scriptlet: incrementa a variável a cada requisição.
- `<%= ... %>`: expressão JSP, imprime o valor na página (atalho para `out.print`).

### Considerações de Concorrência
- O container geralmente usa uma única instância do Servlet para múltiplas requisições simultâneas (threads). Variáveis de instância precisam de sincronização se houver escrita concorrente. Evite estados mutáveis em JSP.

---

## 4.3 Expressões: Exibição de Valores <a id="43-expressoes"></a>

### Código de Referência
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

### Explicação
- `<%@page import="..."%>`: permite usar classes sem FQN no código JSP.
- `<%= ... %>`: avalia e imprime o resultado (converte para `String`).
- Bloco scriptlet define variável usada na expressão seguinte.

### Boas Práticas
- Em vez de `<%= ... %>`, prefira `${...}` com EL para separar lógica e apresentação.

---

## 4.4 Objetos Implícitos <a id="44-objetos-implicitos"></a>

### Código de Referência
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
            Integer contadorSessao = (Integer) session.getAttribute("meuContador");
            if (contadorSessao == null) {
                contadorSessao = 1;
            } else {
                contadorSessao++;
            }
            session.setAttribute("meuContador", contadorSessao);
        %>
        
        <p>Você (nesta sessão) visitou esta página <strong><%= contadorSessao %></strong> vezes.</p>
        <p><em>Abra em aba anônima e compare!</em></p>
        
    </body>
</html>
```

### Explicação
- Objetos implícitos: `request`, `response`, `session`, `application`, entre outros, fornecidos pelo container.
- `request.getRemoteAddr()`, `request.getMethod()`: informações da requisição atual.
- Sessão: armazena e recupera atributos por usuário (persistem entre requisições daquele usuário enquanto a sessão existir).

### Dicas
- Para dados compartilhados entre todos os usuários, use `application`.
- Evite armazenar objetos pesados na sessão.

---

## 5.1 Diretivas (`page`, `include`, `taglib`) <a id="51-diretivas"></a>

### Componentes Reutilizáveis: Cabeçalho e Rodapé
```jsp
<%-- cabecalho.jsp --%>
<div style="background-color: #f0f0f0; padding: 10px; border-bottom: 1px solid #ccc;">
    <h1>Meu Site Dinâmico</h1>
    <p>Bem-vindo ao nosso curso de JSP!</p>
</div>
<hr>
```
```jsp
<%-- rodape.jsp --%>
<hr>
<div style="text-align: center; font-size: 12px; color: #666;">
    <p>&copy; 2023 - Escola Técnica XYZ. Todos os direitos reservados.</p>
</div>
```

### Página Principal com `include` e `page`
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
        <p>Exemplo de `page` (import) e `include` (composição estática).</p>
        <%
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

### Explicação
- `page import`: importa classes Java para uso na JSP.
- `include (diretiva)`: inclui o conteúdo do arquivo-alvo na fase de tradução (inclusão estática). Útil para cabeçalho/rodapé.
- Alternativa dinâmica: `<jsp:include ...>` inclui em tempo de requisição.

---

## 5.2 Ações JSP com JavaBeans <a id="52-acoes"></a>

### JavaBean `Aluno`
```java
package br.com.escola.modelo;

public class Aluno implements java.io.Serializable {
    private String nome;
    private int matricula;

    public Aluno() {}

    public String getNome() { return nome; }
    public void setNome(String nome) { this.nome = nome; }
    public int getMatricula() { return matricula; }
    public void setMatricula(int matricula) { this.matricula = matricula; }
}
```

### Formulário HTML
```html
<form action="processa_aluno.jsp" method="POST">
    Nome: <input type="text" name="nome" /><br/><br/>
    Matrícula: <input type="text" name="matricula" /><br/><br/>
    <input type="submit" value="Enviar Dados" />
</form>
```

### Processamento com Ações JSP
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
    <jsp:useBean id="alunoBean" class="br.com.escola.modelo.Aluno" scope="request" />
    <jsp:setProperty name="alunoBean" property="*" />
    <h3>Dados do Aluno:</h3>
    <p>
      Nome: <jsp:getProperty name="alunoBean" property="nome" /><br/>
      Matrícula: <jsp:getProperty name="alunoBean" property="matricula" />
    </p>
    <p><em>(Sem scriptlets!)</em></p>
  </body>
</html>
```

### Explicação
- `<jsp:useBean>`: procura no escopo especificado e cria se não existir.
- `scope="request"`: o bean vive durante a requisição corrente.
- `<jsp:setProperty property="*">`: mapeia automaticamente parâmetros de formulário para propriedades do Bean com nomes equivalentes (chama setters).
- `<jsp:getProperty>`: lê propriedade via getter e imprime.

---

## 5.3 Expression Language (EL) <a id="53-el"></a>

### Reescrevendo o Processamento com EL
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
      Nome: ${alunoBean.nome}<br/>
      Matrícula: ${alunoBean.matricula}
    </p>
    <p><strong>Nome em maiúsculas:</strong> ${alunoBean.nome.toUpperCase()}</p>
  </body>
</html>
```

### Explicação
- `${alunoBean.nome}`: a EL resolve a propriedade chamando `getNome()`.
- Acesso a escopos: `${requestScope.x}`, `${sessionScope.y}`, `${applicationScope.z}`.
- Operadores e funções: EL permite operações simples e chamada de métodos públicos.

### Boas Práticas
- Use EL para ler dados; mantenha lógica de negócio fora da JSP.

---

## 5.4 JSTL – Biblioteca de Tags Padrão <a id="54-jstl"></a>

### Dependência (Maven)
```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
```

### Página com Lista de Alunos
```jsp
<%@page import="java.util.ArrayList"%>
<%@page import="br.com.escola.modelo.Aluno"%>
<%@page import="java.util.List"%>
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
      List<Aluno> listaDeAlunos = new ArrayList<>();
      Aluno a1 = new Aluno(); a1.setNome("João da Silva"); a1.setMatricula(101);
      Aluno a2 = new Aluno(); a2.setNome("Maria Oliveira"); a2.setMatricula(102);
      Aluno a3 = new Aluno(); a3.setNome("Pedro Santos"); a3.setMatricula(103);
      listaDeAlunos.add(a1); listaDeAlunos.add(a2); listaDeAlunos.add(a3);
      request.setAttribute("alunos", listaDeAlunos);
    %>

    <c:if test="${not empty alunos}">
      <table>
        <tr>
          <th>Matrícula</th>
          <th>Nome</th>
        </tr>
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

### Explicação
- `<%@taglib ... %>`: registra biblioteca Core da JSTL com prefixo `c`.
- `<c:if>`: renderização condicional declarativa.
- `<c:forEach>`: iteração sobre coleções (equivalente ao `for` da linguagem, mas sem scriptlets).
- `<c:out>`: imprime escapando HTML por padrão (mitiga XSS).

### Observações
- Em aplicações reais, a lista deve vir de um Servlet/serviço. A JSP apenas itera e exibe.
- Evite criar listas em scriptlet como no exemplo didático.

---

## Boas Práticas e Dicas <a id="boas-praticas"></a>

- Separe responsabilidades:
  - Servlet/Controller: prepara modelos e decide navegação.
  - JSP: renderiza a visão com EL/JSTL.
- Minimize scriptlets; prefira EL + JSTL.
- Reutilize com diretivas `include` ou `jsp:include` para cabeçalhos/rodapés.
- Cuide de concorrência: evite estado mutável em JSP/Servelts sem sincronização.
- Configure corretamente `contentType` e `charset` para evitar problemas de acentuação.
- Para internacionalização e formatação avançada, utilize JSTL fmt (`<fmt:...>`).

---

Este documento detalha, passo a passo, as práticas do Módulo 3, no mesmo estilo da explicação aprofundada de Servlets, servindo como material de estudo e referência rápida durante o desenvolvimento com JSP.
