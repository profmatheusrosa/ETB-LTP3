# Módulo 1: Fundamentos de Aplicações Web

## Boas-vindas ao Desenvolvimento Web!
Olá, futuro(a) desenvolvedor(a)!
Neste módulo, vamos mergulhar no universo das aplicações web. Você já parou para pensar em como a mágica acontece quando você digita www.google.com no seu navegador e, em segundos, uma página inteira aparece na sua tela?

Vamos desvendar todos esses segredos, desde a estrutura básica da comunicação na internet até a construção da parte visual de um site. Ao final deste módulo, você entenderá os pilares que sustentam praticamente toda a web que usamos hoje.

Preparado? Vamos começar!

## 1. Aplicação Web
### 1.1 Arquitetura e Funcionamento
**Conceito Geral**

Uma Aplicação Web é um software que funciona em um servidor e é acessado pelo usuário através de um navegador de internet (como Chrome, Firefox, etc.). Diferente de um programa que você instala no seu computador (como o Word ou um jogo), a aplicação web fica "hospedada" na internet.

Pense em uma rede social, um e-commerce ou seu webmail. Todos são exemplos de aplicações web.

**Interação Cliente-Servidor**
A web funciona com base em um modelo chamado Cliente-Servidor. É como uma conversa entre duas partes:
- **Cliente (Client):** É o seu navegador. Ele é responsável por pedir as informações. Quando você digita um endereço e aperta Enter, o cliente está fazendo um pedido.
- **Servidor (Server):** É um computador superpotente que fica ligado 24/7 em algum lugar do mundo. Ele é responsável por armazenar os arquivos do site (textos, imagens, códigos) e responder aos pedidos do cliente.

A interação funciona assim:
1. **Requisição (Request):** O Cliente (seu navegador) envia uma "requisição" ao Servidor, pedindo uma página. Ex: "Ei, servidor do site X, me mande a sua página inicial!".
2. **Processamento:** O Servidor recebe o pedido, encontra os arquivos necessários (HTML, CSS, imagens) ou executa algum código para gerar a página.
3. **Resposta (Response):** O Servidor envia de volta para o Cliente uma "resposta" contendo todo o conteúdo da página.
4. **Renderização:** O Cliente (navegador) recebe essa resposta e "monta" a página na sua tela para que você possa vê-la e interagir com ela.

#### Exercícios: Arquitetura e Funcionamento
No modelo Cliente-Servidor, qual é o papel principal do Cliente?

- a) Armazenar os arquivos do site e processar dados.
- b) Fazer requisições e exibir as respostas para o usuário.
- c) Garantir a segurança da conexão com a internet.
- d) Gerenciar o banco de dados da aplicação.
<details>
<summary>Resposta</summary>
<p><b>b) Fazer requisições e exibir as respostas para o usuário.</b> O cliente (navegador) é a ferramenta que o usuário utiliza para solicitar e visualizar as páginas web.</p>
</details>

### 1.2 Cliente Web
O Cliente Web mais comum que usamos todos os dias é o navegador (ou browser).

**Exemplos de Navegadores:**
- Google Chrome
- Mozilla Firefox
- Microsoft Edge
- Safari

**Funções Básicas de um Navegador**
Um navegador é um programa muito inteligente. Suas principais funções são:
- **Fazer Requisições:** Enviar pedidos (usando o protocolo HTTP, que veremos a seguir) para os servidores web.
- **Interpretar e Renderizar:** Ele "lê" os códigos que o servidor envia (HTML, CSS, JavaScript) e os transforma na página visual que você vê na tela.
  - O HTML define a estrutura (títulos, parágrafos, links).
  - O CSS define a aparência (cores, fontes, layout).
  - O JavaScript define o comportamento (animações, interações).
- **Gerenciar Recursos:** Ele cuida de cookies (pequenos dados salvos no seu computador), cache (para carregar sites mais rápido) e segurança.

Resumindo: o navegador é o seu tradutor e portal para o conteúdo da web.

#### Exercícios: Cliente Web
Qual das seguintes opções descreve a principal função de um navegador web?

- a) Apenas armazenar arquivos para acesso offline.
- b) Criar e hospedar sites na internet.
- c) Interpretar linguagens como HTML, CSS e JavaScript para exibir uma página web.
- d) Proteger o computador contra todos os tipos de vírus.
<details>
<summary>Resposta</summary>
<p><b>c) Interpretar linguagens como HTML, CSS e JavaScript para exibir uma página web.</b> Essa é a sua função central: transformar código em uma experiência visual e interativa.</p>
</details>

### 1.3 Servidor Web
O Servidor Web é o "cérebro" e o "armazém" por trás de um site. É um software rodando em um computador (o servidor físico) que tem duas funções principais:
- **Armazenar os arquivos:** Todos os componentes de um site – arquivos HTML, imagens, vídeos, folhas de estilo CSS, scripts JavaScript, etc. – ficam guardados no servidor.
- **Responder às requisições HTTP:** Ele fica "escutando" a internet, esperando por pedidos dos clientes. Quando um pedido chega, ele encontra o recurso solicitado e o envia de volta como uma resposta HTTP.

**Exemplos de Softwares de Servidor Web**
Os mais famosos e utilizados no mundo são:
- Apache HTTP Server: Um dos mais antigos, robusto e muito popular.
- Nginx (pronuncia-se "Engine-Ex"): Conhecido por sua alta performance e eficiência, especialmente para sites com muitos acessos simultâneos.
- Microsoft IIS (Internet Information Services): A solução da Microsoft, integrada ao sistema operacional Windows Server.

**Hospedagem (Hosting)**
Você não precisa ter um supercomputador em casa para ter um site. As empresas de hospedagem oferecem o serviço de "alugar" um espaço em seus servidores. Você simplesmente envia seus arquivos para lá, e a empresa se encarrega de manter o servidor funcionando, seguro e conectado à internet.

#### Exercícios: Servidor Web
Qual é a função primária de um software de servidor web como o Apache ou o Nginx?

- a) Criar o design visual das páginas web.
- b) Traduzir o código JavaScript para o navegador.
- c) Armazenar os arquivos de um site e responder às requisições dos clientes.
- d) Navegar na internet e exibir sites para o usuário final.
<details>
<summary>Resposta</summary>
<p><b>c) Armazenar os arquivos de um site e responder às requisições dos clientes.</b> O servidor guarda o conteúdo e o entrega quando solicitado.</p>
</details>

### 1.4 Protocolo HTTP
Se o Cliente e o Servidor são duas pessoas que precisam conversar, o HTTP (Hypertext Transfer Protocol) é o idioma que eles usam. É um conjunto de regras que define como as mensagens (requisições e respostas) devem ser formatadas para que ambos se entendam.

- **HTTP:** A versão padrão.
- **HTTPS (Hypertext Transfer Protocol Secure):** É a versão segura do HTTP. A comunicação é criptografada, garantindo que ninguém no meio do caminho consiga ler os dados. Sempre que você vê um cadeado na barra de endereço, o site está usando HTTPS.

**Estrutura de uma Mensagem HTTP**
1. **Requisição (Request)** - O que o cliente envia:
   - **Método (Method):** Diz o que o cliente quer fazer. Os mais comuns são:
     - GET: Para buscar dados. É o que o navegador usa quando você acessa um site.
     - POST: Para enviar dados. Usado quando você preenche um formulário de login ou de cadastro.
   - **URL:** O endereço do recurso que está sendo solicitado (ex: /contato/index.html).
   - **Cabeçalhos (Headers):** Informações extras, como o tipo de navegador que está fazendo o pedido, os idiomas que ele aceita, etc.
2. **Resposta (Response)** - O que o servidor devolve:
   - **Código de Status (Status Code):** Um número de 3 dígitos que diz se o pedido deu certo.
     - 200 OK: Deu tudo certo, aqui está o que você pediu.
     - 404 Not Found: Não encontrei o que você pediu. A famosa página "não encontrada".
     - 500 Internal Server Error: Deu um problema aqui no servidor, não consigo te atender agora.
   - **Cabeçalhos (Headers):** Informações extras sobre a resposta, como o tipo de conteúdo (HTML, imagem, etc.).
   - **Corpo (Body):** O conteúdo em si (o código HTML da página, a imagem, etc.).

#### Exercícios: Protocolo HTTP
Quando você preenche um formulário de login com seu usuário e senha e clica em "Entrar", qual método HTTP o navegador provavelmente usará para enviar esses dados de forma segura?

- a) GET
- b) POST
- c) DELETE
- d) UPDATE
<details>
<summary>Resposta</summary>
<p><b>b) POST.</b> O método POST é o padrão para enviar dados (como os de um formulário) para o servidor.</p>
</details>

Você tenta acessar uma página que foi removida de um site. Qual código de status HTTP é o mais provável que o servidor retorne?

- a) 200 OK
- b) 301 Moved Permanently
- c) 404 Not Found
- d) 500 Internal Server Error
<details>
<summary>Resposta</summary>
<p><b>c) 404 Not Found.</b> Este é o código padrão para indicar que o recurso solicitado não existe no servidor.</p>
</details>

## 2. Tecnologias do Lado Cliente
Agora que entendemos a "conversa" entre cliente e servidor, vamos focar no que o cliente (navegador) recebe e precisa interpretar. Essas são as três tecnologias fundamentais que constroem praticamente qualquer página web.

Pense nelas como a construção de um corpo humano:
- **XHTML/HTML:** O esqueleto (estrutura).
- **CSS:** A pele, cabelo e roupas (aparência).
- **JavaScript:** O cérebro e os músculos (comportamento e interação).

### 2.1 Marcação XHTML
XHTML (eXtensible HyperText Markup Language) é uma linguagem de marcação que serve para estruturar o conteúdo de uma página. Ele usa tags para definir o que cada parte do conteúdo é: um título, um parágrafo, uma imagem, um link, etc.

O XHTML é uma versão mais "rígida" e bem-formatada do HTML. Hoje em dia, usamos o HTML5, que é mais flexível, mas aprender as regras do XHTML é uma ótima forma de criar código limpo e organizado.

**Sintaxe e Estrutura Básica**
- **Tags:** São escritas entre < e >. Ex: `<p>`.
- **Elementos:** Geralmente compostos por uma tag de abertura (`<p>`) e uma de fechamento (`</p>`). O conteúdo fica no meio.
- **Atributos:** Informações extras dentro da tag de abertura. Ex: `<a href="https://google.com">`.

**Regras importantes do XHTML (Boas Práticas para HTML5):**
- As tags devem ser sempre fechadas. Ex: `<p>...</p>`. Tags "vazias" fecham em si mesmas: `<br />`, `<hr />`, `<img ... />`.
- As tags e atributos devem estar em letras minúsculas.
- Os valores dos atributos devem estar sempre entre aspas. Ex: `width="100"`.
- O documento deve ter uma estrutura aninhada corretamente (uma tag aberta dentro de outra deve ser fechada antes da tag externa).

**Exemplo de Estrutura Mínima:**
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" lang="pt-br">
<head>
  <title>Minha Primeira Página</title>
</head>
<body>
  <h1>Olá, Mundo!</h1>
  <p>Este é o meu primeiro parágrafo em XHTML.</p>
  <a href="outra_pagina.html">Isso é um link!</a>
</body>
</html>
```

#### Exercícios: Marcação XHTML
Qual das seguintes linhas de código está escrita de acordo com as regras de sintaxe do XHTML?

- a) `<IMG SRC="foto.jpg">`
- b) `<p>Este é um parágrafo`
- c) `<br>`
- d) `<a href="pagina.html">Clique aqui</a>`
<details>
<summary>Resposta</summary>
<p><b>d) `<a href="pagina.html">Clique aqui</a>`</b>. As outras opções estão incorretas porque: a) usa tags e atributos maiúsculos; b) não fecha a tag `<p>`; c) a tag `<br>` não está auto-fechada (`<br />`).</p>
</details>

### 2.2 Estilos CSS
CSS (Cascading Style Sheets) é uma linguagem de estilo usada para definir a aparência dos elementos XHTML/HTML. Com CSS, você controla cores, fontes, espaçamentos, posicionamento e muito mais.

Ele separa o conteúdo (HTML) da apresentação (CSS), o que torna o código muito mais organizado e fácil de manter.

**Como funciona?**
O CSS funciona com base em regras. Cada regra tem duas partes:
- **Seletor (Selector):** Define quais elementos HTML serão estilizados.
- **Declaração (Declaration):** Define como eles serão estilizados, contendo uma ou mais duplas de propriedade: valor;.

```css
/* Isso é um comentário em CSS */

/* Seletor de tag 'p' */
p {
  color: blue; /* Propriedade 'color' com valor 'blue' */
  font-size: 16px; /* Propriedade 'font-size' com valor '16px' */
}

/* Seletor de ID '#titulo-principal' */
#titulo-principal {
  background-color: #f0f0f0;
  border-bottom: 2px solid black;
}

/* Seletor de classe '.caixa-destaque' */
.caixa-destaque {
  padding: 20px;
  border: 1px dotted gray;
}
```

- **Seletor de Tag:** Aplica o estilo a todas as tags daquele tipo (ex: h1, p, div).
- **Seletor de Classe:** Aplica a todos os elementos que tiverem o atributo `class="nome-da-classe"`. É reutilizável.
- **Seletor de ID:** Aplica a um único elemento que tenha o atributo `id="nome-do-id"`. O ID deve ser único na página.

#### Exercícios: Estilos CSS
Você quer que todos os parágrafos (`<p>`) de seu site tenham o texto na cor vermelha. Qual regra CSS você usaria?

- a) p { text-color: red; }
- b) .p { color: red; }
- c) p { color: red; }
- d) #p { background-color: red; }
<details>
<summary>Resposta</summary>
<p><b>c) `p { color: red; }`</b>. O seletor de tag é apenas `p` (sem ponto ou #), e a propriedade para a cor do texto é `color`.</p>
</details>

### 2.3 Scripts JavaScript
JavaScript (JS) é uma linguagem de programação que roda diretamente no navegador. Ela é usada para criar interatividade, dinamismo e comportamento nas páginas web.

Se o HTML é o esqueleto e o CSS é a aparência, o JavaScript é o que dá vida à página.

**O que o JavaScript pode fazer?**
- **Manipulação de DOM:** O DOM (Document Object Model) é a representação da estrutura HTML da página em forma de árvore de objetos. O JavaScript pode acessar, modificar, adicionar ou remover qualquer elemento ou conteúdo dessa árvore.
  - Exemplo: Mudar o texto de um parágrafo quando um botão é clicado.
- **Eventos:** O JS pode "ouvir" e reagir a ações do usuário, como cliques (`onclick`), movimento do mouse (`onmouseover`), pressionamento de teclas (`onkeydown`), etc.
- **Validações:** Verificar se os campos de um formulário foram preenchidos corretamente antes de enviá-los para o servidor.
- **Animações:** Criar efeitos visuais e animações complexas.
- **Requisições Assíncronas (AJAX):** Buscar ou enviar dados para o servidor em segundo plano, sem precisar recarregar a página inteira.

**Exemplo simples:**

HTML:
```html
<p id="mensagem">Olá!</p>
<button onclick="mudarMensagem()">Clique em mim</button>
```
JavaScript:
```javascript
function mudarMensagem() {
  // Acessa o elemento pelo ID 'mensagem'
  var elemento = document.getElementById("mensagem");

  // Muda o conteúdo HTML dentro do elemento
  elemento.innerHTML = "Você clicou no botão!";
}
```

Neste exemplo, quando o botão é clicado (`onclick`), a função `mudarMensagem()` é executada. Essa função usa o JavaScript para encontrar o parágrafo com `id="mensagem"` e alterar seu conteúdo.

#### Exercícios: Scripts JavaScript
Qual é o principal propósito do JavaScript em uma aplicação web?

- a) Definir a estrutura e o conteúdo da página.
- b) Estilizar a página com cores, fontes e layouts.
- c) Adicionar interatividade e comportamento dinâmico à página.
- d) Armazenar os dados da aplicação no servidor.
<details>
<summary>Resposta</summary>
<p><b>c) Adicionar interatividade e comportamento dinâmico à página.</b> Essa é a função essencial do JavaScript no lado cliente.</p>
</details>

O que significa "Manipulação de DOM" em JavaScript?

- a) Enviar dados para o servidor de forma segura.
- b) Alterar a estrutura, o estilo ou o conteúdo de um documento HTML depois que ele foi carregado.
- c) Criar regras de estilo para aplicar aos elementos da página.
- d) Definir o endereço IP do servidor web.
<details>
<summary>Resposta</summary>
<p><b>b) Alterar a estrutura, o estilo ou o conteúdo de um documento HTML depois que ele foi carregado.</b> O DOM é a representação da página, e o JS pode "manipulá-la" para criar efeitos dinâmicos.</p>
</details>

---

## Parabéns!
Você concluiu a primeira parte do nosso módulo! Agora você entende como a web funciona nos bastidores, desde o pedido do seu navegador até a montagem da página na sua tela, e conhece as três tecnologias essenciais que formam a base do desenvolvimento front-end. Continue estudando e praticando!
