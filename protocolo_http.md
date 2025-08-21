# 1.4 Protocolo HTTP: Métodos, Cabeçalhos, Requisição e Resposta

## O que é HTTP?
O HTTP (HyperText Transfer Protocol) é o protocolo usado para comunicação entre navegadores e servidores web. Ele define como as mensagens são formatadas e transmitidas, e como os servidores e navegadores devem responder a diferentes comandos.

## Como funciona o HTTP?
Quando você acessa um site, seu navegador faz uma **requisição HTTP** para o servidor. O servidor então responde com uma **resposta HTTP** contendo os dados solicitados (por exemplo, uma página HTML).

---

## Métodos HTTP
Os métodos HTTP indicam a ação que queremos realizar. Os principais são:

- **GET**: Solicita dados do servidor (exemplo: acessar uma página).
- **POST**: Envia dados para o servidor (exemplo: enviar um formulário).
- **PUT**: Atualiza dados existentes no servidor.
- **DELETE**: Remove dados do servidor.

Exemplo de requisição GET:
```
GET /index.html HTTP/1.1
Host: www.exemplo.com
```

---

## Cabeçalhos HTTP
Os cabeçalhos são informações extras enviadas junto com a requisição ou resposta. Eles ajudam a identificar detalhes como tipo de conteúdo, idioma, autenticação, etc.

Exemplo de cabeçalhos em uma requisição:
```
Host: www.exemplo.com
User-Agent: Mozilla/5.0
Accept: text/html
```

Exemplo de cabeçalhos em uma resposta:
```
Content-Type: text/html
Content-Length: 305
```

---

## Estrutura de uma Requisição HTTP
Uma requisição HTTP geralmente tem:
1. **Linha de método** (ex: GET /index.html HTTP/1.1)
2. **Cabeçalhos** (ex: Host, User-Agent)
3. **Corpo** (opcional, usado em métodos como POST)

---

## Estrutura de uma Resposta HTTP
Uma resposta HTTP geralmente tem:
1. **Linha de status** (ex: HTTP/1.1 200 OK)
2. **Cabeçalhos** (ex: Content-Type)
3. **Corpo** (conteúdo solicitado, como HTML, imagem, etc.)

Exemplo de resposta:
```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 305

<html>
  <body>
    <h1>Bem-vindo!</h1>
  </body>
</html>
```

---

## Exercícios

## Exercícios (Múltipla Escolha)

1. O que significa a sigla HTTP?
   a) HyperText Transfer Protocol
   b) Home Transfer Protocol
   c) Hyper Terminal Text Process
   d) Host Transfer Type
   <details><summary>Resposta</summary>
   a) HyperText Transfer Protocol
   </details>

2. Qual dos métodos abaixo é usado para enviar dados ao servidor?
   a) GET
   b) POST
   c) DELETE
   d) PUT
   <details><summary>Resposta</summary>
   b) POST
   </details>

3. Sobre os cabeçalhos HTTP, assinale a alternativa correta:
   a) São usados apenas em respostas do servidor
   b) Servem para enviar informações extras na requisição ou resposta
   c) Só existem no método GET
   d) São obrigatórios apenas em formulários
   <details><summary>Resposta</summary>
   b) Servem para enviar informações extras na requisição ou resposta
   </details>

4. Qual a principal diferença entre uma requisição e uma resposta HTTP?
   a) A requisição é enviada pelo servidor e a resposta pelo navegador
   b) A requisição contém o conteúdo solicitado e a resposta apenas cabeçalhos
   c) A requisição é enviada pelo navegador e a resposta pelo servidor
   d) Não existe diferença
   <details><summary>Resposta</summary>
   c) A requisição é enviada pelo navegador e a resposta pelo servidor
   </details>

5. Qual das opções representa corretamente uma requisição GET e uma resposta HTTP?
   a) GET /index.html HTTP/1.1\nHost: www.exemplo.com
      HTTP/1.1 200 OK\nContent-Type: text/html
   b) POST /index.html HTTP/1.1\nHost: www.exemplo.com
      HTTP/1.1 404 Not Found\nContent-Type: text/html
   c) DELETE /index.html HTTP/1.1\nHost: www.exemplo.com
      HTTP/1.1 500 Internal Server Error\nContent-Type: text/html
   d) PUT /index.html HTTP/1.1\nHost: www.exemplo.com
      HTTP/1.1 201 Created\nContent-Type: text/html
   <details><summary>Resposta</summary>
   a) GET /index.html HTTP/1.1\nHost: www.exemplo.com
      HTTP/1.1 200 OK\nContent-Type: text/html
   </details>
