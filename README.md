# Sistema de Login com Spring Boot, MySQL e BCrypt

Projeto didático de login com autenticação por sessão. As senhas são guardadas no banco como hash **BCrypt**, nunca em texto puro. A tela de login envia email e senha para o Java, que consulta o MySQL, confere o hash e cria uma sessão para o usuário logado.

## Tecnologias

- Java
- Spring Boot (Spring Web)
- MySQL
- JDBC
- BCrypt (biblioteca `jbcrypt`)
- HTML, CSS e JavaScript puros (sem frameworks no front-end)

## Estrutura do projeto

| Arquivo | Descrição |
|---|---|
| [pom.xml](pom.xml) | Configuração do Maven e dependências do projeto |
| [banco.sql](banco.sql) | Script que cria o banco `meu_db` e a tabela `usuarios` |
| [DemoApplication.java](demo3/src/main/java/com/example/demo/DemoApplication.java) | Classe principal que inicia o servidor Spring Boot |
| [LoginController.java](src/main/java/com/example/demo/LoginController.java) | Endpoints `/login`, `/usuario` e `/logout` |
| [UsuarioDAO.java](src/main/java/com/example/demo/UsuarioDAO.java) | Acesso ao banco: busca o hash da senha pelo email |
| [SenhaUtil.java](src/main/java/com/example/demo/SenhaUtil.java) | Criptografia (BCrypt): gerar hash e conferir senha |
| [GerarHash.java](src/main/java/com/example/demo/GerarHash.java) | Utilitário temporário que gera o `INSERT` de um usuário com senha criptografada |
| [criptografia.html](src/main/resources/static/criptografia.html) | Tela de login (`/criptografia.html`) |
| [home.html](src/main/resources/static/home.html) | Tela principal, acessível só com login feito (`/home.html`) |
| [launch.json](.vscode/launch.json) | Configurações de execução para o VS Code |

> Os caminhos acima seguem o padrão do Maven/Spring Boot. Se seus arquivos estiverem em outra pasta, ajuste os links.

## Como funciona

1. O usuário preenche email e senha em `criptografia.html`.
2. O JavaScript envia os dados para `POST /login`.
3. O `LoginController` pede ao `UsuarioDAO` o hash salvo para aquele email.
4. O `SenhaUtil` compara a senha digitada com o hash usando BCrypt.
5. Se estiver correto, o email é guardado na sessão e o navegador vai para `home.html`.
6. Ao abrir `home.html`, a página chama `GET /usuario`. Sem sessão, o usuário volta para o login.
7. O botão **Sair** chama `POST /logout`, que encerra a sessão.

## Endpoints

| Método | Caminho | Função |
|---|---|---|
| `POST` | `/login` | Recebe `email` e `senha`, valida e cria a sessão |
| `GET` | `/usuario` | Retorna o email do usuário logado ou `401` |
| `POST` | `/logout` | Encerra a sessão |

## Como executar

### 1. Criar o banco de dados

Execute o [banco.sql](banco.sql) no MySQL (Workbench, DBeaver ou terminal). Ele cria o banco `meu_db` e a tabela `usuarios`.

### 2. Configurar a conexão

No [UsuarioDAO.java](src/main/java/com/example/demo/UsuarioDAO.java), ajuste `USER` e `PASS` para o usuário e a senha do seu MySQL.

### 3. Criar um usuário

Edite o email e a senha em [GerarHash.java](src/main/java/com/example/demo/GerarHash.java) e execute a classe. Ela imprime um `INSERT` no console. Copie esse comando e execute no MySQL.

### 4. Iniciar o servidor

Execute a classe [DemoApplication.java](src/main/java/com/example/demo/DemoApplication.java).

### 5. Acessar o sistema

Abra no navegador:

```
http://localhost:8080/criptografia.html
```

## Observações de segurança

Este projeto é para estudo. Antes de usar em produção, considere:

- **Não envie senhas reais no código.** A senha do MySQL e a senha do usuário de teste estão escritas diretamente nos arquivos.
- **Não use o usuário `root` na aplicação.** Crie um usuário específico com permissões mínimas.
- **Use HTTPS** para proteger as credenciais em trânsito.
- **Não suba arquivos compilados** (`*.class`) nem a pasta `target/` para o GitHub. Use um `.gitignore`.
