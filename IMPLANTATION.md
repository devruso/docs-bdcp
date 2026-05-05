# Documentação de Implantação

- Implantação 

| Requisitos Mínimos (recomendado para desenvolvimento) | Descrição |
| --------- | --------- |
| `Sistema Operacional` | Linux Ubuntu v20.04 |
| `Memória RAM` | 8gb |
| `Browser WEB` | Google Chrome |

| Tecnologias | Descrição |
| --------- | --------- |
| `Engine` | NodeJs v16.13.2 |
| `Gerenciador de Pacotes` | NPM v8.1.2 |
| `Banco de Dados` | Postgresql |
| `Container` | Docker v20.01 |

| Acessos | Descrição |
| --------- | --------- |
| `Acesso Local Frontend` | localhost:3000 |
| `Acesso Local Backend` | localhost:3333 |
| `Documentação API` | localhost:3333/api/docs |

---

### Requisitos Obrigatórios
1. Para executar este projeto localmente, você precisa primeiramente ter o [NodeJs](https://nodejs.org/en/) v16 instalado na sua máquina. Naturalmente, o NPM será instalado junto.
2. Você também precisará ter instalado em sua máquina o [Docker](https://www.docker.com/).
3. Caso ainda não tenha esses recursos em sua máquina ou tem dúvidas de que foi instalado corretamente, busque pela documentação oficial e depois volte para este material de implementação.

### Get Started
1. Faça o clone dos repositórios backend e frontend respectivamente abaixo:

```sh
  $ git clone https://github.com/MATE85-Grupo4-20221/api-bdcp.git
  $ git clone https://github.com/MATE85-Grupo4-20221/app-bdcp.git
```

2. Executando a Aplicação Backend:
 ```sh
    # Abra o repositório em seu editor preferido
    $ cd /api-bdcp
    
    # Mude para a branch de desenvolvimento
    $ git checkout dev
    
    # Faça uma cópia do arquivo .env.example e renomeie-o apenas para .env
    # É neste arquivo que estão definidas as variáveis de conexão com o banco. 
    $ cp .env.example .env
 
    # Instale as dependências
    $ npm install
    
    # Execute o comando abaixo apenas para garantir que está tudo certo com o eslint.
    $ npm run lint:fix
    
    # Execute o script de criação do banco de dados postgres.
    # Nesta etapa é imprescindível que tenha instalado o docker em sua máquina. 
    # Caso não tenha e opte por fazer a configuração manual do banco, não esqueça de checar as variáveis de configuração no arquivo .env
    $ npm run postgres:create
    
    # Finalmente, execute o comando para rodar a aplicação, que ficará disponível em localhost:3333
    $ npm run dev
 ```
  - Se tudo der certo, esta deve ser a visão do seu cmd.
  - ![cmd-api](https://user-images.githubusercontent.com/62779767/170699050-988336f6-f206-4a37-b74a-e9fceb02a998.png)

3. Executando a Aplicação Frontend:
 ```sh
    # Abra o repositório em seu editor preferido
    $ cd /app-bdcp
    
    # Mude para a branch de desenvolvimento
    $ git checkout develop
    
    # Faça uma cópia do arquivo .env.example e renomeie-o apenas para .env
    # É neste arquivo que estão definidas as variáveis de conexão com a API.
    $ cp .env.example .env
 
    # Instale as dependências
    $ npm install
    
    # Execute o comando abaixo apenas para garantir que está tudo certo com o eslint.
    $ npm run lint:fix
    
    # Finalmente, execute o comando para rodar a aplicação, que ficará disponível em localhost:3000
    $ npm run start
 ```
   - Se tudo der certo, esta deve ser a visão do seu cmd.
   - ![cmd-app](https://user-images.githubusercontent.com/62779767/170700192-326fb6e3-4851-4f27-8da4-462560cf333e.png)


 
 ### Next Steps
  1. Para ter acesso à todo o conteúdo da aplicação você deve criar um usuário (se cadastrar no sistema). Utilize de softwares como, por exemplo, [Insomnia](https://insomnia.rest/download) ou [Postman](https://www.postman.com/), acessando diretamente o endpoint de geração de link de cadastro.
  2. Abra o Insomnia e envie uma requisição do tipo **get** para http://localhost:3333/api/invite/generate e depois copie o token gerado.
  - ![insomnia-invite-user](https://user-images.githubusercontent.com/62779767/170702107-d0cb39bf-0c26-4aa6-9af3-5143e518e550.png)
  3. Abra outra guia no Insomnia e envie uma requisição do tipo **post** para http://localhost:3333/api/users/token sendo que no lugar de 'token' você deve escrever o hash gerado anteriormente na rota de invite.
  - ![insomnia-register-user](https://user-images.githubusercontent.com/62779767/170703586-0883f5f1-b4e0-4159-9e26-90f30a7d56b2.png)
  4. A partir disso você já consegue logar no sistema e utilizar de todas as funcionalidades.
  5. Acesse o endpoint http://localhost:3333/api/docs para saber como usar todos os endpoints da API e como integrar ao frontend.

### Observações
 - Todo o processo de instalação dos softwares, clonagem de repositório e instalação de dependências não deve ultrapassar um tempo médio de 10 minutos. Caso esse tempo seja ultrapassado, algo deu errado e, neste caso, você deve voltar o passo e tentar novamente.
 - Em caso de dúvida, consulte um dos desenvolvedores através do email do projeto 'bdcpicufba@gmail.com'.

---

### Primeira Carga Operacional Pelo Frontend
1. Garanta que a stack esteja ativa com backend em `localhost:3333` e frontend em `localhost:3000`.
2. Entre com um usuário administrativo institucional da UFBA.
3. Acesse a tela `Adicionar disciplina`.
4. No bloco `Importação SIAC`, preencha exatamente:
  - `Código do curso`: `112140`
  - `Semestre vigente`: `20132`
5. Clique em `Importar do SIAC` e aguarde o resumo operacional.
  - Evidência validada em ambiente ativo: `requested=36`, `created=36`, `failed=0`.
6. No bloco `Importação SIGAA público`, preencha exatamente:
  - `Tipo de fonte`: `Programa`
  - `ID da fonte`: `1820`
  - `Nível acadêmico`: `Mestrado`
7. Clique em `Importar do SIGAA público` e aguarde o resumo operacional.
  - Evidência validada em ambiente ativo: `requested=77`, `created=77`, `failed=0`.
8. Após cada importação, volte para a listagem de disciplinas para conferir os códigos inseridos e abrir a página de detalhe.

### Códigos Confirmados Em Validação Real
- SIAC: `MATA67`.
- SIGAA público: `PGCOMP001`.

### Recuperação de Texto com Acentuação Corrompida
1. Antes de refazer cargas do SIGAA público em uma base já utilizada, execute no backend o comando `npm run repair:encoding`.
2. O script corrige em lote campos textuais já persistidos em `components` e `component_drafts` quando houver mojibake UTF-8 interpretado como Latin-1, como `TÃ³picos` ou `PÃ³s-GraduaÃ§Ã£o`.
3. Após o reparo, execute novamente a importação pelo frontend apenas se desejar complementar disciplinas novas ainda não cadastradas.
4. Para validação rápida, abra uma disciplina SIGAA já importada e confirme a presença correta de acentos em nome e departamento.

### Auditoria Preventiva de Encoding
1. Para localizar automaticamente possíveis resíduos de mojibake em tabelas textuais, execute `npm run audit:encoding` no backend.
2. O relatório inclui totais por entidade (`components`, `component_drafts`, `component_logs`, `users`) e amostras com prévia original e prévia reparada sugerida.
3. Se `affectedFields` for maior que zero em alguma entidade, priorize rodar novamente `npm run repair:encoding` e revisar manualmente as amostras retornadas.

---

