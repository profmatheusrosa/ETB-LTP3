Excelente! Dando continuidade ao nosso curso, vamos agora para um dos tópicos mais importantes do desenvolvimento web: a arquitetura MVC. Este módulo vai organizar tudo o que aprendemos e nos ensinar a construir aplicações robustas e fáceis de manter.

## Módulo de Estudo: Arquitetura MVC e Padrões de Projeto

### Objetivo
Compreender e aplicar a arquitetura Model-View-Controller (MVC) para organizar aplicações web Java. Aprender a separar as responsabilidades em camadas distintas: Modelo (regras de negócio), Visão (apresentação) e Controle (fluxo da aplicação), utilizando o padrão de projeto Data Access Object (DAO) para a persistência de dados.

### Ambiente
- **IDE**: NetBeans 8.2
- **Servidor de Aplicação**: GlassFish
- **Projeto Base**: Continuaremos com o projeto AulaJSP ou, preferencialmente, criaremos um novo chamado `LojaMVC` para manter o código organizado.

## 6. Arquitetura MVC e Padrões de Projeto

### 6.1 Visão Geral

#### Teoria
Até agora, misturamos um pouco de lógica Java, HTML e até acesso a dados dentro dos nossos arquivos JSP. Para projetos pequenos, isso pode funcionar, mas em aplicações reais, isso gera o que chamamos de "código macarrão" (spaghetti code): um emaranhado de código difícil de ler, de dar manutenção e de trabalhar em equipe.

A arquitetura Model-View-Controller (MVC) é um padrão de projeto que resolve esse problema dividindo a aplicação em três camadas interconectadas, cada uma com uma responsabilidade clara:

- **Model (Modelo)**: A camada que representa os dados e as regras de negócio da aplicação. É o "cérebro". Ela não sabe como os dados serão exibidos.
- **View (Visão)**: A camada responsável pela apresentação dos dados ao usuário. É a "face" da aplicação (nossas páginas JSP). Ela não contém regras de negócio.
- **Controller (Controlador)**: A camada que atua como intermediário. Ele recebe as requisições do usuário, aciona o Modelo para processar os dados e, em seguida, seleciona a Visão apropriada para exibir o resultado. É o "maestro" da orquestra.

**Benefícios do MVC:**
- **Organização**: O código fica limpo e cada parte tem seu lugar.
- **Manutenção Facilitada**: Precisa mudar o layout? Mexa apenas na View. Precisa mudar uma regra de negócio? Mexa apenas no Model.
- **Reutilização de Código**: As regras de negócio no Model podem ser usadas por diferentes Views.
- **Trabalho em Equipe**: Designers podem trabalhar na View (HTML/CSS) enquanto desenvolvedores back-end trabalham no Model e no Controller.

#### Prática Guiada
Vamos criar um novo projeto para aplicar essa arquitetura do zero.

1. No NetBeans, crie um novo projeto Java Web > Aplicação Web.
2. Nome do projeto: `LojaMVC`.
3. Servidor: GlassFish.
4. Versão Java EE: Java EE 7 Web.
5. Clique em Finalizar.

Nos próximos tópicos, vamos construir as camadas deste projeto.

#### Exercícios de Múltipla Escolha (6.1)
Qual é o principal objetivo da arquitetura MVC?

a) Fazer o site carregar mais rápido.

b) Separar as responsabilidades da aplicação em camadas distintas para melhorar a organização e manutenção.

c) Permitir o uso de mais código Java nas páginas JSP.

d) Conectar a aplicação a múltiplos bancos de dados ao mesmo tempo.

<details>
<summary>Resposta</summary>
<p>b) Separar as responsabilidades da aplicação em camadas distintas para melhorar a organização e manutenção.</p>
</details>

### 6.2 Camada Modelo (Model)

#### Teoria
O Modelo é o coração da sua aplicação. Ele consiste em:

- **Classes de Entidade (ou JavaBeans)**: Classes Java que representam os objetos do seu domínio. Ex: Produto, Cliente, Pedido. Elas contêm os dados (atributos) e os métodos get/set.
- **Regras de Negócio**: Lógicas que governam a aplicação. Ex: "um produto não pode ter preço negativo", "um cliente não pode ser excluído se tiver pedidos em aberto". Essas regras podem estar nas próprias classes de entidade ou em classes de serviço.

#### Prática Guiada
Vamos criar a classe de entidade `Produto` para nossa loja virtual.

1. No projeto `LojaMVC`, clique com o botão direito em Pacotes de Códigos-Fonte.
2. Vá em Novo > Pacote Java.... Dê o nome `br.com.loja.modelo`.
3. Agora, clique com o botão direito sobre o pacote recém-criado `br.com.loja.modelo`.
4. Vá em Novo > Classe Java....
5. Nome da classe: `Produto`.
6. Insira o seguinte código. É um JavaBean simples, como já vimos.

```java
package br.com.loja.modelo;

public class Produto implements java.io.Serializable {

    private int id;
    private String nome;
    private double preco;

    // Construtor padrão
    public Produto() {
    }

    // Getters e Setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public double getPreco() {
        return preco;
    }

    public void setPreco(double preco) {
        // Exemplo de uma regra de negócio simples
        if (preco < 0) {
            this.preco = 0;
        } else {
            this.preco = preco;
        }
    }
}
```

#### Exercícios de Múltipla Escolha (6.2)
Na arquitetura MVC, qual das seguintes opções melhor descreve a responsabilidade da camada Modelo?

a) Exibir formulários HTML e tabelas de dados.

b) Receber requisições HTTP do navegador.

c) Conter os dados e as regras de negócio da aplicação.

d) Controlar o fluxo de navegação entre as páginas.

<details>
<summary>Resposta</summary>
<p>c) Conter os dados e as regras de negócio da aplicação.</p>
</details>

### 6.3 Camada de Acesso a Dados (DAO)

#### Teoria
Como o Modelo interage com o banco de dados? Poderíamos colocar o código de acesso a dados (SQL) diretamente nas classes do Modelo, mas isso cria um acoplamento forte. Se mudarmos de banco de dados, teremos que alterar o Modelo inteiro.

Para resolver isso, usamos o padrão de projeto DAO (Data Access Object). O DAO é uma classe cuja única responsabilidade é se comunicar com a fonte de dados (banco de dados, arquivo, etc.). Ele abstrai os detalhes da persistência.

As operações básicas que um DAO geralmente implementa são chamadas de CRUD:

- **Create (Criar/Inserir)**
- **Read (Ler/Consultar)**
- **Update (Atualizar/Editar)**
- **Delete (Excluir/Remover)**

#### Prática Guiada
Vamos criar um DAO para nossos produtos. Para simplificar e focar no padrão, vamos simular um banco de dados usando uma `ArrayList` estática em memória.

1. Crie um novo pacote Java chamado `br.com.loja.dao`.
2. Dentro deste pacote, crie uma interface chamada `ProdutoDAO`. Uma interface define um "contrato" que a classe concreta deve seguir.

```java
// br.com.loja.dao.ProdutoDAO.java (Interface)
package br.com.loja.dao;

import br.com.loja.modelo.Produto;
import java.util.List;

public interface ProdutoDAO {
    void adicionar(Produto produto);
    List<Produto> listarTodos();
    Produto buscarPorId(int id);
    void atualizar(Produto produto);
    void remover(int id);
}
```

3. Agora, crie uma classe chamada `ProdutoDAOImpl` que implementa essa interface.

```java
// br.com.loja.dao.ProdutoDAOImpl.java (Classe)
package br.com.loja.dao;

import br.com.loja.modelo.Produto;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.atomic.AtomicInteger;

public class ProdutoDAOImpl implements ProdutoDAO {

    // Simulação de um banco de dados em memória
    private static final List<Produto> repositorio = new ArrayList<>();
    // Simulação de uma sequence para gerar IDs únicos
    private static final AtomicInteger contadorId = new AtomicInteger(0);

    @Override
    public void adicionar(Produto produto) {
        produto.setId(contadorId.incrementAndGet()); // Gera um novo ID
        repositorio.add(produto);
        System.out.println("Produto adicionado: " + produto.getNome());
    }

    @Override
    public List<Produto> listarTodos() {
        System.out.println("Listando todos os " + repositorio.size() + " produtos.");
        return new ArrayList<>(repositorio); // Retorna uma cópia para segurança
    }

    @Override
    public Produto buscarPorId(int id) {
        for (Produto p : repositorio) {
            if (p.getId() == id) {
                return p;
            }
        }
        return null; // Retorna nulo se não encontrar
    }

    @Override
    public void atualizar(Produto produto) {
        for (int i = 0; i < repositorio.size(); i++) {
            if (repositorio.get(i).getId() == produto.getId()) {
                repositorio.set(i, produto);
                System.out.println("Produto atualizado: " + produto.getNome());
                return;
            }
        }
    }

    @Override
    public void remover(int id) {
        repositorio.removeIf(p -> p.getId() == id);
        System.out.println("Produto com id " + id + " removido.");
    }
}
```

#### Exercícios de Múltipla Escolha (6.3)
Qual é a principal vantagem de se usar o padrão DAO?

a) Aumentar a performance do banco de dados.

b) Isolar a lógica de acesso a dados do resto da aplicação, facilitando a troca da fonte de dados.

c) Garantir que todos os dados sejam criptografados.

d) Criar automaticamente as tabelas no banco de dados.

<details>
<summary>Resposta</summary>
<p>b) Isolar a lógica de acesso a dados do resto da aplicação, facilitando a troca da fonte de dados.</p>
</details>

### 6.4 Camada Visão (View)

#### Teoria
A Visão é a camada de apresentação. Em nosso contexto, ela será composta por arquivos JSP. A regra de ouro da Visão no MVC é: ela deve ser "burra". Isso significa que ela não deve conter nenhuma regra de negócio ou lógica de acesso a dados. Sua única função é receber dados do Controlador e exibi-los ao usuário, preferencialmente usando Expression Language (EL) e JSTL.

#### Prática Guiada
Vamos criar duas JSPs: uma para listar os produtos e outra para o formulário de cadastro.

1. No NetBeans, clique com o botão direito em Páginas Web e crie um novo arquivo Página JSP... chamado `lista-produtos.jsp`.

```jsp
<%-- /Páginas Web/lista-produtos.jsp --%>
<%@taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Lista de Produtos</title>
    <style>table, th, td { border: 1px solid black; border-collapse: collapse; padding: 8px; }</style>
</head>
<body>
    <h1>Nossos Produtos</h1>
    
    <c:if test="${not empty produtos}">
        <table>
            <tr>
                <th>ID</th>
                <th>Nome</th>
                <th>Preço</th>
            </tr>
            <c:forEach var="produto" items="${produtos}">
                <tr>
                    <td><c:out value="${produto.id}"/></td>
                    <td><c:out value="${produto.nome}"/></td>
                    <td><c:out value="${produto.preco}"/></td>
                </tr>
            </c:forEach>
        </table>
    </c:if>

    <c:if test="${empty produtos}">
        <p>Nenhum produto cadastrado.</p>
    </c:if>
    
    <br/>
    <a href="form-produto.jsp">Adicionar Novo Produto</a>
</body>
</html>
```

2. Agora, crie a página `form-produto.jsp`.

```jsp
<%-- /Páginas Web/form-produto.jsp --%>
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Cadastro de Produto</title>
</head>
<body>
    <h1>Novo Produto</h1>
    <%-- O formulário enviará os dados para o nosso Controlador --%>
    <form action="ProdutoController" method="POST">
        <%-- Usamos um campo oculto para dizer ao Controlador qual ação queremos executar --%>
        <input type="hidden" name="acao" value="cadastrar" />
        
        Nome: <input type="text" name="nome" required /><br/><br/>
        Preço: <input type="number" name="preco" step="0.01" required /><br/><br/>
        
        <input type="submit" value="Salvar Produto" />
    </form>
</body>
</html>
```

#### Exercícios de Múltipla Escolha (6.4)
Em uma aplicação MVC bem estruturada, o que uma página JSP (a Visão) NÃO deve fazer?

a) Exibir dados usando EL e JSTL.

b) Conter formulários HTML para entrada de dados do usuário.

c) Acessar diretamente o banco de dados para buscar informações.

d) Receber dados que foram colocados no escopo da requisição pelo Controlador.

<details>
<summary>Resposta</summary>
<p>c) Acessar diretamente o banco de dados para buscar informações.</p>
</details>

### 6.5 Camada Controle (Controller)

#### Teoria
O Controlador é o componente que liga tudo. Em aplicações Java EE, o Servlet é a tecnologia perfeita para atuar como Controlador.

Sua responsabilidade é:
- Receber a requisição do cliente (navegador).
- Interpretar a requisição (ex: o usuário quer listar produtos ou cadastrar um novo?).
- Chamar as classes do Modelo (incluindo o DAO) para executar a lógica necessária.
- Colocar os dados resultantes em um escopo acessível pela Visão (geralmente o request).
- Encaminhar (forward) a requisição para a Visão (JSP) apropriada para renderizar a resposta.

#### Prática Guiada
Vamos criar nosso Servlet Controlador.

1. Crie um novo pacote Java chamado `br.com.loja.controle`.
2. Clique com o botão direito neste pacote e vá em Novo > Servlet....
3. Nome da Classe: `ProdutoController`.
4. Clique em Próximo e depois em Finalizar (não precisa alterar as configurações de URL).
5. Modifique o código do Servlet para o seguinte:

```java
package br.com.loja.controle;

import br.com.loja.dao.ProdutoDAO;
import br.com.loja.dao.ProdutoDAOImpl;
import br.com.loja.modelo.Produto;
import java.io.IOException;
import java.util.List;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

// A anotação @WebServlet mapeia a URL que ativará este Servlet
@WebServlet(name = "ProdutoController", urlPatterns = {"/ProdutoController"})
public class ProdutoController extends HttpServlet {

    private final ProdutoDAO dao = new ProdutoDAOImpl();

    protected void processRequest(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        
        // Usamos um parâmetro 'acao' para determinar o que fazer
        String acao = request.getParameter("acao");
        
        if (acao == null) {
            acao = "listar"; // Ação padrão
        }

        switch (acao) {
            case "listar":
                // 1. Chama o DAO para obter a lista de produtos
                List<Produto> produtos = dao.listarTodos();
                // 2. Coloca a lista no escopo da requisição
                request.setAttribute("produtos", produtos);
                // 3. Encaminha para a JSP
                request.getRequestDispatcher("/lista-produtos.jsp").forward(request, response);
                break;
                
            case "cadastrar":
                // 1. Pega os dados do formulário
                String nome = request.getParameter("nome");
                double preco = Double.parseDouble(request.getParameter("preco"));
                
                // 2. Cria um objeto Produto
                Produto novoProduto = new Produto();
                novoProduto.setNome(nome);
                novoProduto.setPreco(preco);
                
                // 3. Salva no "banco de dados" através do DAO
                dao.adicionar(novoProduto);
                
                // 4. Redireciona para a listagem para mostrar o novo produto
                // Usamos redirect para evitar reenvio do formulário ao atualizar a página (Padrão PRG)
                response.sendRedirect("ProdutoController?acao=listar");
                break;
                
            // Aqui você poderia adicionar os casos "editar", "remover", etc.
                
            default:
                // Se a ação não for reconhecida, apenas lista os produtos
                response.sendRedirect("ProdutoController?acao=listar");
                break;
        }
    }

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        processRequest(request, response);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        processRequest(request, response);
    }
}
```

#### Testando a Aplicação Completa
1. Crie um arquivo `index.html` na raiz de Páginas Web para ser a porta de entrada.

```html
<!DOCTYPE html>
<html>
<head>
    <title>Loja MVC</title>
    <meta http-equiv="refresh" content="0;url=ProdutoController?acao=listar">
 </head>
<body>
    <p>Redirecionando para a lista de produtos...</p>
</body>
</html>
```

2. Execute o projeto `LojaMVC` (clique com o botão direito no projeto e Executar).
3. O `index.html` irá redirecioná-lo para `ProdutoController?acao=listar`.
4. O `doGet` do Servlet será chamado, a lista (vazia no início) será buscada e a página `lista-produtos.jsp` será exibida.
5. Clique em "Adicionar Novo Produto". Você será levado para `form-produto.jsp`.
6. Preencha os dados e clique em "Salvar". O formulário será submetido via POST para o `ProdutoController`.
7. O `doPost` executará a ação "cadastrar", que criará o produto e redirecionará você de volta para a ação "listar".
8. A página `lista-produtos.jsp` será exibida novamente, agora com o produto que você acabou de cadastrar!

Você acabou de construir uma aplicação web completa seguindo a arquitetura MVC!

#### Exercícios de Múltipla Escolha (6.5)
Qual é a função principal de um Servlet quando atua como Controlador no padrão MVC?

a) Armazenar os dados da aplicação em memória.

b) Gerar o HTML final que será enviado ao navegador.

c) Escrever as regras de negócio, como validação de campos.

d) Receber requisições, interagir com o Modelo e selecionar a Visão apropriada.

<details>
<summary>Resposta</summary>
<p>d) Receber requisições, interagir com o Modelo e selecionar a Visão apropriada.</p>
</details>

