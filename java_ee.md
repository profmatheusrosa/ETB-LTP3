# Java EE (Jakarta EE)

## Sumário
- [Objetivo](#objetivo)
- [1. Visão Geral — Como Java EE se encaixa na Web](#1-visão-geral--como-java-ee-se-encaixa-na-web)
- [2. Componentes Principais (visão prática)](#2-componentes-principais-visão-prática)
- [3. Ciclo básico de uma requisição em Java EE](#3-ciclo-básico-de-uma-requisição-em-java-ee)
- [4. Exemplo mínimo — Servlet (conceito e código simples)](#4-exemplo-mínimo--servlet-conceito-e-código-simples)
- [5. Ferramentas e fluxo de trabalho](#5-ferramentas-e-fluxo-de-trabalho)
- [Glossário (termos essenciais)](#glossário-termos-essenciais)
- [Exercícios de Fixação](#exercícios-de-fixação)


## Objetivo
Apresentar os conceitos básicos de Java EE (atualmente conhecido como Jakarta EE) aplicados ao desenvolvimento de aplicações web, usando como base os conteúdos sobre aplicações web (cliente-servidor, HTTP, HTML).

---

## 1. Visão Geral — Como Java EE se encaixa na Web
- A web funciona com o modelo Cliente-Servidor: o navegador (cliente) faz requisições HTTP e o servidor responde. Java EE é uma plataforma para criar o lado servidor dessas aplicações.
- Enquanto HTML/CSS/JS constroem a interface no cliente, Java EE fornece componentes e APIs para processar requisições, acessar banco de dados e expor funcionalidades (páginas dinâmicas, APIs REST, etc.).
- Java EE = conjunto de especificações (Servlets, JPA, JAX-RS, CDI, etc.) que são implementadas por servidores de aplicação (ex.: WildFly, Payara) ou contêineres web (ex.: Apache Tomcat para servlets simples).

### Por que aprender Java EE?
- É uma tecnologia consolidada em empresas e ótima para entender como aplicações web profissionais funcionam no servidor.
- Conecta conceitos já estudados: HTTP, requisições GET/POST, páginas dinâmicas e armazenamento persistente.

---

## 2. Componentes Principais (visão prática)
- Servlets
  - São classes Java que processam requisições HTTP (do tipo GET, POST, etc.).
  - Funcionam como o "controlador" que recebe a requisição, faz processamento e retorna resposta (HTML, JSON, redirecionamento).
  - Exemplo: um Servlet responde a `/login` e valida credenciais.

- JSP / Templates
  - JSP (JavaServer Pages) permite gerar HTML dinâmico no servidor misturando código e marcação. Hoje muitos projetos usam templates (Thymeleaf) ou frameworks MVC, mas JSP é útil para aprender o básico.

- JAX-RS (REST)
  - API para criar endpoints REST (JSON) facilmente. Útil para criar APIs que um frontend (ou outro sistema) consome.

- JPA (Java Persistence API)
  - Padrão para acesso a banco de dados usando entidades Java (ORM). Simplifica salvar, buscar e consultar dados.

- CDI (Context and Dependency Injection)
  - Mecanismo para injetar dependências e gerenciar componentes do aplicativo.

- EJB (Enterprise JavaBeans)
  - Fornece componentes que executam regras de negócio (transações, concorrência).

---

## 3. Ciclo básico de uma requisição em Java EE
1. O navegador envia uma requisição HTTP (GET/POST) para o servidor.
2. O servidor recebe a requisição e a encaminha para um Servlet.
3. O código servidor processa parâmetros, chama serviços (possível acesso ao banco via JPA).
4. O servidor retorna uma resposta: HTML (página), redirecionamento, ou JSON (API).

---

## 4. Exemplo mínimo — Servlet (conceito e código simples)
Descrição: um Servlet que responde a GET e mostra uma mensagem.

```java
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/ola")
public class OlaServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        resp.setContentType("text/html; charset=UTF-8");
        resp.getWriter().write("<h1>Olá do Servlet!</h1>");
    }
}
```
---

## 5. Ferramentas e fluxo de trabalho
- IDEs: Eclipse, IntelliJ IDEA, VS Code (com extensões).
- Build: Maven ou Gradle (Maven é muito usado em exemplos didáticos).
- Servidor para testes: Apache Tomcat (servlets/JSP), Payara ou WildFly (implementam muitas APIs Java EE).
- Empacotamento: `.war` (aplicação web). Em servidores completos pode usar `.ear` (aplicações empresariais).

---

## Glossário (termos essenciais)
- Servlet: Componente Java que processa requisições HTTP e produz respostas (HTML, JSON, redirecionamento).
- WAR: Web Application Archive — arquivo empacotado (`.war`) que contém a aplicação web para deploy em um servidor.
- REST: Estilo arquitetural para criar APIs baseadas em recursos e que usam verbs HTTP (GET, POST, PUT, DELETE).
- JSON: Formato leve de troca de dados (texto) muito usado em APIs REST.
- JPA: Java Persistence API — especificação para mapear entidades Java a tabelas de banco de dados (ORM).
- CDI: Injeção de dependência e gerenciamento de contexto em aplicações Java EE.
- EJB: Enterprise JavaBeans — componentes de servidor que podem lidar com transações e concorrência.

---

## Exercícios de Fixação

### 1. Múltipla Escolha
1. Qual componente Java EE é responsável por processar requisições HTTP diretamente?
   - a) JPA
   - b) Servlet
   - c) EJB
   - d) CDI
   <details>
   <summary>Resposta</summary>
   <p><b>b) Servlet</b>. Servlets são os componentes básicos que processam requisições HTTP em Java EE.</p>
   </details>

2. Em uma arquitetura web Java EE, qual afirmação é INCORRETA?
   - a) JSP gera conteúdo HTML dinamicamente
   - b) JPA acessa o banco de dados
   - c) Servlets processam requisições HTTP
   - d) EJB é necessário para todo acesso a banco de dados
   <details>
   <summary>Resposta</summary>
   <p><b>d) EJB é necessário para todo acesso a banco de dados</b>. JPA pode ser usado diretamente sem EJB para acesso a dados.</p>
   </details>

3. Sobre JAX-RS, é correto afirmar:
   - a) Substitui completamente os Servlets
   - b) É usado apenas para páginas JSP
   - c) Facilita a criação de APIs REST
   - d) É obrigatório em todo projeto Java EE
   <details>
   <summary>Resposta</summary>
   <p><b>c) Facilita a criação de APIs REST</b>. JAX-RS é específico para criar endpoints REST de forma simples.</p>
   </details>


---
