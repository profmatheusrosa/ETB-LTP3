# ETB-LTP3

## CALENDÁRIO APÓS A2

| **Data** | **Turma C** | **Turma D** |
|----------|-------------|-------------|
| 27/10 | Grupo 1 | Grupo 1 |
| 30/10 | Grupo 2 | Grupo 2 |
| 03/11 | Grupo 3 | Grupo 3 |
| 06/11 | Grupo 4 | Grupo 4 |
| 10/11 | Grupo 5 | Grupo 5 |
| 13/11 | Grupo 1 | Grupo 1 |
| 17/11 | Grupo 2 | Grupo 2 |
| 20/11 | Grupo 3 | Grupo 3 |
| 24/11 | Grupo 4 | Grupo 4 |
| 27/11 | Grupo 5 | Grupo 5 |
| 01/12 | Grupo 1 | Grupo 1 |
| 04/12 | Grupo 2 | Grupo 2 |
| 08/12 | Grupo 3 | Grupo 3 |



## PRÉ-REQUISITOS E CONFIGURAÇÃO DO AMBIENTE

### **Ferramentas Necessárias:**

| **Componente** | **Versão** | **Descrição** |
|----------------|------------|---------------|
| **Java JDK** | **8** | Java Development Kit versão 8 (LTS) |
| **NetBeans** | **8.2** | IDE oficial para desenvolvimento Java EE |
| **Servidor** | **GlassFish 4.1.1** | Servidor de aplicação Java EE 7 |

### **Plataforma Java:**
- **Java EE 7/8** - Plataforma oficial para este curso
- **Compatível com Jakarta EE 8** - Futuro da plataforma
- **Não usar J2EE** - Plataforma obsoleta e complexa

###  **Downloads:**

- **Java JDK 8 para Windows x64:** [Download Java JDK 8](https://javadl.oracle.com/webapps/download/GetFile/1.8.0_202-b08/1961070e4c9b4e26a04e7f5a083f551e/windows-i586/jdk-8u202-windows-x64.exe)
- **NetBeans 8.2 com GlassFish integrado para Windows x64:** [Download NetBeans 8.2 Java EE](https://dlc-cdn.sun.com/netbeans/8.2/final/bundles/netbeans-8.2-javaee-windows.exe)

### **Importante:**
- Use **exclusivamente** Java JDK 8 para este curso
- O NetBeans 8.2 é a versão oficial recomendada
- GlassFish 4.1.1 é o servidor padrão para desenvolvimento Java EE 7

---

## Integridade Acadêmica

Vivemos em uma sociedade que, com razão, critica fortemente a desonestidade, a corrupção e as condutas antiéticas. No entanto, é importante refletir que esses grandes desvios começam, muitas vezes, em pequenos atos do dia a dia — quando deixamos de fazer nossa parte, quando copiamos um exercício, pedimos a alguém para resolver um trabalho por nós ou usamos a inteligência artificial para evitar o esforço que deveria ser nosso.

Neste curso, cada exercício, projeto e desafio é uma oportunidade de aprendizado real. É ali que erramos, corrigimos e evoluímos. Quando alguém opta por copiar respostas, usar indevidamente ferramentas de inteligência artificial ou deixar que outros façam o que deveria ser seu esforço, está, na verdade, enganando a si mesmo.

A integridade não é uma virtude que se adquire subitamente na vida adulta; ela se cultiva nas decisões aparentemente insignificantes de cada dia. Quando você escolhe enfrentar a dificuldade de um exercício ao invés de simplesmente copiá-lo, está fortalecendo não apenas suas habilidades técnicas, mas seu caráter.

Portanto, convidamos cada aluno a trilhar seu caminho com integridade acadêmica — porque é isso que forma não apenas bons profissionais, mas cidadãos conscientes, capazes de transformar a realidade que tanto desejam ver melhorada.

---

## CONTEÚDO DO CURSO
| **Módulo**   | **Tópicos Principais**                       | **Subtópicos**                                                                                   | **Detalhes**                                                                 |
|--------------|----------------------------------------------|--------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
| **Módulo 1** | 1. Aplicação Web                             | 1.1 Arquitetura e Funcionamento                                                                  | Conceito geral, interação cliente-servidor                                   |
|              |                                              | 1.2 Cliente Web                                                                                  | Navegadores, funções básicas                                                 |
|              |                                              | 1.3 Servidor Web                                                                                 | Funções, exemplos, hospedagem                                                |
|              |                                              | 1.4 Protocolo HTTP                                                                               | Métodos, cabeçalhos, requisição e resposta                                   |
|              | 2. Tecnologias do Lado Cliente               | 2.1 Marcação XHTML                                                                                | Estrutura, sintaxe, boas práticas                                            |
|              |                                              | 2.2 Estilos CSS                                                                                  | Seletores, propriedades, layout                                              |
|              |                                              | 2.3 Scripts JavaScript                                                                           | Manipulação de DOM, eventos, validações                                     |
| **Módulo 2** | 3. Servlets                                   | 3.1 Definição                                                                                     | Conceito e finalidade                                                         |
|              |                                              | 3.2 Ciclo de Vida                                                                                 | init, service, destroy                                                        |
|              |                                              | 3.3 Tratamento de Solicitações GET e POST                                                         | Diferenças e usos                                                             |
|              |                                              | 3.4 Parâmetros e Respostas                                                                       | Obtenção de parâmetros, envio de respostas                                  |
|              |                                              | 3.5 Redirecionamento e Encaminhamento                                                            | sendRedirect, forward                                                         |
|              |                                              | 3.6 Seções                                                                                        | Criação, uso, gerenciamento de sessão                                        |
| **Módulo 3** | 4. Java Server Pages (JSP) – Parte 1          | 4.1 Scriptlets                                                                                    | Código Java embutido                                                          |
|              |                                              | 4.2 Declarações                                                                                   | Definição de variáveis e métodos                                              |
|              |                                              | 4.3 Expressões                                                                                    | Exibição de valores                                                           |
|              |                                              | 4.4 Objetos Implícitos                                                                            | request, response, session, application                                      |
|              | 5. Java Server Pages (JSP) – Parte 2          | 5.1 Diretivas                                                                                     | page, include, taglib                                                          |
|              |                                              | 5.2 Ações                                                                                         | `<jsp:useBean>`, `<jsp:setProperty>`, etc.                                    |
|              |                                              | 5.3 Linguagem de Expressão – EL                                                                   | Sintaxe, operadores, integração com JSP                                      |
|              |                                              | 5.4 Biblioteca de Tags Padrão – JSTL                                                              | Core, format, SQL, funções                                                    |
| **Módulo 4** | 6. Arquitetura MVC e Padrões de Projeto       | 6.1 Visão Geral                                                                                   | Conceito, benefícios                                                          |
|              |                                              | 6.2 Camada Modelo                                                                                 | Classes Java, entidades, regras de negócio                                   |
|              |                                              | 6.3 Camada de Acesso a Dados (DAO)                                                                | Padrão DAO, CRUD                                                              |
|              |                                              | 6.4 Camada Visão                                                                                  | JSP para exibição de dados                                                    |
|              |                                              | 6.5 Camada Controle                                                                               | Servlets controlando fluxo e lógica                                          |
| **Módulo 5** | 7. Projeto de Conclusão de Curso              | 7.1 Desenvolvimento                                                                               | Integração de todos os conceitos: MVC, DAO, JSP, Servlets, Cliente e Servidor |



