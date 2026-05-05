# Planejamento de Testes

### Definições:
- Tecnologias
  - Jest and Supertest
- Ponto de Partida
  - Testes Unitários (validar os inputs e o comportamento dos métodos com seus respectivos retornos e tratamentos de erros).
- Método
  - Execução manual em ambiente local.
  - Execução automatizada em gitflow, realizada via script a cada deploy.
- Justificativa
  - A quantidade estimada de testes é justificada pelo número de rotas expostas considerando, no mínimo, um teste para o bom comportamento e o mau comportamento desses componentes.

| Testes Unitários | Módulo de Disciplinas |
|---------|-----------------|
| *Quantidade Estimada* | 5 |
| *Prioridade* | Altíssima |

- **`should be able to create a imported component`**: Para que esse teste passe, sua aplicação deve criar os componentes através da importação do SIAC, utilizando a rota do crawler. Lembre-se que, de acordo à arquitetura do projeto, quando uma disciplina é criada uma cópia também é criada na tabela de draft.
- **`should be able to create a component`**: Para que esse teste passe, sua aplicação deve criar com sucesso e retornar um objeto do tipo disciplina ou código 201. Lembre-se que, de acordo à arquitetura do projeto, quando uma disciplina é criada uma cópia também é criada na tabela de draft.
- **`should not be able to create component with same code`**: Para que esse teste passe, sua aplicação deve retornar 400 ao tentar criar um novo objeto do tipo disciplina que contenha um código igual ao de outro objeto disciplina já existente.
- **`should not be able to create new Component without auth user`**: Para que esse teste passe, sua aplicação deve retornar 400 ao tentar criar um novo objeto do tipo disciplina com um usuário não autenticado.
- **`should not be able to create new component with empty body`**: Para que esse teste passe, sua aplicação deve retornar 400 ao receber uma requisição vazia.
- **`should be able to list the components`**: Para que esse teste passe, sua aplicação deve retornar um array de disciplinas.
- **`should be able to list the component by id`**: Para que esse teste passe, sua aplicação deve retornar um objeto do tipo disciplina referente a disciplina encontrada para o respectivo id.
- **`should be able to list the components filtered by name or code`**: Para que esse teste passe, sua aplicação deve retornar um array de disciplinas que deem match com um nome ou código.
- **`should be able to search published disciplines without accent marks`**: Para que esse teste passe, a busca do catálogo público deve retornar disciplinas com acento mesmo quando o termo de busca for enviado sem acentuação.
- **`should be able to preview a draft import from DOCX/PDF`**: Para que esse teste passe, sua aplicação deve receber um arquivo válido autenticado na rota de preview de rascunho e retornar um payload sugerido com os campos reconhecidos e a lista de seções não reconhecidas para revisão humana.
- **`should not be able to preview a draft import without file`**: Para que esse teste passe, sua aplicação deve retornar código 400 quando a rota de preview for chamada sem arquivo multipart.
- **`should not be able to preview a draft import with unsupported file type`**: Para que esse teste passe, sua aplicação deve retornar código 400 quando a rota de preview receber um arquivo diferente de PDF ou DOCX.
- **`should not be able to preview a draft import above file size limit`**: Para que esse teste passe, sua aplicação deve retornar código 400 quando o arquivo exceder o limite configurado de upload.
- **`should be able to export component PDF with approval metadata when available`**: Para que esse teste passe, sua aplicação deve incluir no PDF exportado os dados de aprovação formal da disciplina quando o último log de aprovação existir.
- **`should generate DOCX from current published component data instead of hardcoded template text`**: Para que esse teste passe, o DOCX exportado deve refletir o código, nome, departamento e demais campos oficiais da disciplina publicada, sem reutilizar conteúdo residual do modelo-base.
- **`should not render teacher signature placeholders in official exports`**: Para que esse teste passe, a exportação oficial em DOCX/PDF não deve incluir linhas de assinatura do docente quando o documento institucional depender apenas da aprovação formal registrada.
- **`should persist official version code and snapshot on approval log`**: Para que esse teste passe, ao aprovar um rascunho o sistema deve registrar no log de aprovação o código de versão (`ddMMyyyy + ata`) e o snapshot oficial de ementa/conteúdo programático publicado.
- **`should be able to get component details by code without authentication`**: Para que esse teste passe, a rota pública de detalhes por código deve retornar a disciplina publicada sem exigir token.
- **`should return the exact draft by code even when there are similar codes`**: Para que esse teste passe, ao consultar um código de disciplina em rascunho o backend deve retornar exclusivamente a disciplina com igualdade exata case-insensitive, sem confundir com códigos semelhantes.
- **`should persist draft update log with previous/new values for critical fields`**: Para que esse teste passe, ao alterar rascunho e publicar a disciplina o histórico deve registrar `program` e `workload` com valores anteriores e novos (before/after).
- **`should list active public shares with pagination and sorting`**: Para que esse teste passe, o endpoint de listagem de links ativos por disciplina deve respeitar `page`, `limit`, `sortBy` e `sortOrder`, retornando `results`, `total` e `meta.totalPages`.
- **`should revoke all active shares from a component`**: Para que esse teste passe, ao acionar a revogação em massa o backend deve invalidar todos os links ativos da disciplina e retornar `revokedCount` coerente.
- **`should not allow non-admin user to revoke all active shares`**: Para que esse teste passe, o backend deve negar com 401 tentativas de revogação em massa realizadas por usuário sem elegibilidade administrativa.
- **`should ignore JS/CSS token noise when SIGAA page has no components`**: Para que esse teste passe, o parser SIGAA não pode inferir códigos de disciplina a partir de tokens técnicos (CSS/JSF) quando a página pública não contém componentes curriculares.
- **`should extract component rows from tabular SIGAA-like HTML`**: Para que esse teste passe, o parser SIGAA deve extrair código/nome/departamento e remover duplicatas quando houver linhas tabulares válidas de componentes.
- **`should skip existing component and preserve stored data during SIGAA import`**: Para que esse teste passe, a importação pública do SIGAA deve marcar a disciplina existente como `skippedExisting` sem sobrescrever nome, ementa, pré-requisito ou demais campos já persistidos no banco.
- **`should reject invalid SIGAA component payload and keep database unchanged`**: Para que esse teste passe, registros do SIGAA com código inválido devem ser rejeitados com falha contabilizada, sem criação parcial e sem alteração dos dados existentes.
- **`should categorize batch failures by validation reason during SIGAA import`**: Para que esse teste passe, a importação em lote deve retornar `failureCategories` com contagem por causa raiz (ex.: `invalid_code`, `invalid_name`) para apoiar diagnóstico operacional e monitoramento.
- **`should keep batch import invariant on repeated execution`**: Para que esse teste passe, uma segunda execução com o mesmo lote deve manter contagem e conteúdo persistido invariantes, resultando em `created=0` e `skippedExisting` igual ao total do lote.
- **`should categorize timeout during source collection and continue import on fallback source`**: Para que esse teste passe, falhas externas na coleta (ex.: timeout) devem ser categorizadas em `failureCategories` sem interromper a importação quando houver fonte alternativa disponível.
- **`deve bloquear email fora do domínio UFBA no login`** (frontend): Para que esse teste passe, a UI de autenticação deve impedir envio de credenciais quando o e-mail não terminar com `@ufba.br`.
- **`deve importar SIAC e SIGAA e exibir resumo operacional na interface`** (frontend): Para que esse teste passe, a UI de cadastro deve apresentar contadores (`requested`, `created`, `skippedExisting`, `failed`) e `failureCategories` após importações em lote.
- **`should not be able to create user with non-institutional email domain`** (backend): Para que esse teste passe, o servidor deve negar cadastro com e-mail fora do domínio institucional UFBA.
- **`should not be able to login/reset password with non-institutional email domain`** (backend): Para que esse teste passe, o servidor deve bloquear autenticação e reset de senha com domínio externo.
- **`should return standardized error for non-institutional domain on auth/users routes`** (backend HTTP): Para que esse teste passe, as rotas HTTP de autenticação e usuários devem responder com payload padronizado `{ message }` ao rejeitar domínio não institucional.
- **`deve sinalizar quando o modo híbrido por departamento estiver ativo`** (frontend): Para que esse teste passe, a listagem deve informar visualmente quando o filtro por departamento estiver operando com dataset global carregado.
- **`deve agrupar unidades do Instituto de Computação separadas de outras unidades curriculares`** (frontend): Para que esse teste passe, o seletor de departamento deve manter as opções do Instituto de Computação e programas correlatos destacadas de unidades externas sem ocultar disciplinas válidas do currículo.

| Testes Unitários | Módulo de Auth |
|---------|-----------------|
| *Quantidade Estimada* | 3 |
| *Prioridade* | Alta |

- **`should be able to login`**: Para que esse teste passe, sua aplicação deve receber os devidos parâmetros de login e validar a existência de um usuário, retornando então o token autenticado.
- **`should not be able to login user with incorrect email and/or password`**: Para que esse teste passe, sua aplicação deve receber um email ou senha inválidos e retornar o código 400.
- **`should not be able to login user without email`**: Para que esse teste passe, sua aplicação deve receber apenas uma senha e retornar o código 400.
- **`should not be able to login user without password`**: Para que esse teste passe, sua aplicação deve receber apenas um email e retornar o código 400.
- **`should not be able to login user with empty body`**: Para que esse teste passe, sua aplicação deve receber uma requisição vazia e retornar o código 400.
- **`should be able to reset user password`**: Para que esse teste passe, sua aplicação deve receber um email e validar a existência de um usuário, criando uma nova senha aleatória a ser enviada para o email do mesmo.
- **`should not be able to reset password user with incorrect email`**: Para que esse teste passe, sua aplicação deve receber um email e validar a inexistência de um usuário e retornar o código 400.
- **`should be able to create an invite`**: Para que esse teste passe, sua aplicação deve retornar um link válido (com validade de 24h) a ser utilizado para outros usuários se cadastrarem.

| Testes Unitários | Módulo de Usuários |
|---------|-----------------|
| *Quantidade Estimada* | 3 |
| *Prioridade* | Média |

- **`should be able to create a user`**: Para que esse teste passe, sua aplicação deve criar com sucesso e retornar um objeto do tipo usuário ou código 201. Lembre-se que o cadastro de um usuário está atrelado à lógica de Convite para cadastro.
- **`should be able to update a user`**: Para que esse teste passe, sua aplicação deve editar com sucesso as informações de um usuário e retornar o código 200.
- **`should be able to list a user by id`**: Para que esse teste passe, sua aplicação deve retornar um objeto do tipo usuário respectivo ao id passado.

| Testes de Integração | Módulo de Usuário + Disciplina |
|---------|-----------------|
| *Quantidade Estimada* | 5 |
| *Prioridade* | Altíssima |

- Validar o fluxo completo de import preview autenticado seguido de criação/atualização do draft revisado.
- Validar que uma disciplina aprovada preserva rastreabilidade de data e ata/referência de aprovação na exportação oficial.
- Validar governança de compartilhamento público temporário na tela de detalhe com filtros por criador e faixa de expiração.
- Validar paginação e ordenação de links ativos em cenários de alto volume por disciplina.

## Matriz de Rastreabilidade (Requisito -> Teste)

- `E01US05` -> `should be able to update user role as super admin` (frontend) e cenários de autorização em backend para governança de papel.
- `E03US09` -> `should list active public shares with pagination and sorting`, `should revoke all active shares from a component`, `should not allow non-admin user to revoke all active shares`, além dos cenários de filtro e revogação unitária no frontend.
- `E03US10` -> `should ignore JS/CSS token noise when SIGAA page has no components` e `should extract component rows from tabular SIGAA-like HTML` para robustez do crawler público SIGAA.
- `E03US10` -> `should skip existing component and preserve stored data during SIGAA import` e `should reject invalid SIGAA component payload and keep database unchanged` para garantir importação não-destrutiva e validação defensiva de payload público.
- `E03US10` -> `should categorize batch failures by validation reason during SIGAA import` e `should keep batch import invariant on repeated execution` para robustez de operação em lote, observabilidade de erros e idempotência funcional.
- `E03US10` -> `should categorize timeout during source collection and continue import on fallback source` para telemetria de falhas externas e continuidade operacional com fallback de fonte.
- `E01US05` -> validações de e-mail institucional (`@ufba.br`) em frontend e backend para fail-secure de autenticação e cadastro.
- `E03US10` -> fluxo frontend de importação SIAC/SIGAA com leitura de resumo operacional e paginação híbrida por departamento (server + client).
- `E01US05` -> contrato HTTP padronizado de erro para rejeição de domínio não institucional em `/api/auth/*` e `/api/users/*`.

## Evidência de Validação SIGAA Real (Parser) - 2026-05-04

- Fixtures reais coletadas via `Invoke-WebRequest` em `ementas-api/src/tests/fixtures/sigaa`.
- Casos reais usados na regressão:
  - `source-department-1876851.html`: página pública sem turmas (`Nenhuma turma encontrada`), validando rejeição de falso positivo.
  - `source-department-1876885.html`: mesma estrutura sem componentes, usada para inspeção manual de robustez.
  - `source-program-25210.html` e `source-program-43753.html`: resposta pública sem currículo tabular de componentes para extração direta.
- Resultado validado em teste automatizado (`CrawlerSigaaParser.spec.ts`):
  - precisão de rejeição de ruído em caso real sem componente: `100%` (0 itens esperados, 0 extraídos);
  - acerto estrutural no caso controlado tabular: `100%` (2/2 códigos extraídos sem duplicidade).

| Testes de Integração | Módulo de Usuário + Auth |
|---------|-----------------|
| *Quantidade Estimada* | 3 |
| *Prioridade* | Alta |

| Testes Manuais Exploratórios | Todo o Sistema |
|---------|-----------------|
| *Quantidade Estimada* | 5 |
| *Prioridade* | Baixa |

## Evidência de Execução E2E (Docker Compose) - 2026-05-02

- Ambiente: `ementas-api/docker-compose.yml` (serviços `api`, `app`, `postgres`).
- Cenário executado:
  - build e subida da stack com `docker compose up -d --build`;
  - criação de usuário convidado e autenticação na API;
  - importação SIAC com payload real: `{ "cdCurso": 112140, "nuPerCursoInicial": 20132 }`.
- Resultado observado:
  - total de disciplinas antes da importação: `1`;
  - total de disciplinas após importação: `37`;
  - códigos de exemplo importados: `MATA67, MATA66, MATE11, MATA88, MATA65...`.
- Verificação complementar:
  - `GET /api/components/MATA67` retornou `prerequeriments="NAO_SE_APLICA"`, conforme simplificação de pré-requisitos (texto/códigos sem vínculo automático nesta etapa).

## Evidência Final de Acurácia SIGAA (Banca) - 2026-05-04

- Descoberta de fonte por fluxo JSF (`busca_componentes.jsf`) concluída para:
  - DCC/IC: unidade `1114` (graduação), 133 componentes extraídos;
  - DCI/IC: unidade `2440` (graduação), 32 componentes extraídos;
  - PGCOMP/IC: unidade `1820` (stricto/mestrado), 70 componentes extraídos.
- Medição positiva em lote com verdade-terreno manual estratificada (15 amostras):
  - `code`: 100% (15/15)
  - `name`: 100% (15/15)
  - `department`: 100% (15/15)
  - `academicLevel`: 100% (15/15)
- Artefatos de evidência:
  - `ementas-docs/SIGAA_REAL_CASE_MATRIX.md`
  - `ementas-docs/SIGAA_ACCURACY_FINAL_REPORT_2026-05-04.md`
  - `ementas-docs/SIGAA_ACCURACY_SLIDES_2026-05-05.md`
  - `ementas-api/src/tests/fixtures/sigaa/accuracy-results.json`

## Evidência Complementar de Importação Ativa - 2026-05-05

- Ambiente: stack local ativa com `api` em `http://localhost:3333` e frontend operacional.
- SIAC validado novamente com payload real: `{ "cdCurso": 112140, "nuPerCursoInicial": 20132 }`.
- Resultado observado no resumo de importação SIAC:
  - `requested=36`
  - `created=36`
  - `skippedExisting=0`
  - `failed=0`
- SIGAA público validado novamente com payload real: `{ "sourceType": "program", "sourceId": "1820", "academicLevel": "mestrado" }`.
- Resultado observado no resumo de importação SIGAA público:
  - `requested=77`
  - `created=77`
  - `skippedExisting=0`
  - `failed=0`
- Regra consolidada de qualidade textual:
  - a ementa importada por SIAC, SIGAA público e importação documental passa por normalização para texto corrido, removendo enumerações e marcadores antes da persistência.

## Evidência Visual Complementar (Frontend) - 2026-05-05

- Validação por links públicos temporários de disciplinas importadas:
  - SIAC: `MATA66` em `ementas-docs/assets/validation-shared-mata66.png`.
  - SIAC: `MATE11` em `ementas-docs/assets/validation-shared-mate11.png`.
  - SIGAA público: `IC0027` em `ementas-docs/assets/validation-shared-ic0027.png`.
- Validação automatizada de layout institucional e exportação oficial (frontend local atualizado):
  - Listagem com shell atualizado e filtros alinhados: `ementas-docs/assets/validation-list-layout-2026-05-05.png`.
  - Detalhe com mensagem amigável de metadados de aprovação: `ementas-docs/assets/validation-detail-layout-2026-05-05.png`.
- Evidências anteriores mantidas para rastreabilidade:
  - `ementas-docs/assets/validation-shared-mata67.png`.
  - `ementas-docs/assets/validation-shared-ic0001.png`.
  - `ementas-docs/assets/validation-list-mata67.png`.

## Pacote Visual Estratificado Para Banca - 2026-05-05

- Estrato SIAC:
  - `MATA66` em `ementas-docs/assets/validation-shared-mata66.png`.
  - `MATE11` em `ementas-docs/assets/validation-shared-mate11.png`.
  - `MATA88` em `ementas-docs/assets/validation-shared-mata88.png`.
- Estrato SIGAA público:
  - `IC0027` em `ementas-docs/assets/validation-shared-ic0027.png`.
  - `IC0061` em `ementas-docs/assets/validation-shared-ic0061.png`.
- Critério de composição do pacote:
  - três disciplinas provenientes da carga SIAC com conteúdo programático textual preservado;
  - duas disciplinas provenientes do SIGAA público com renderização do fallback oficial quando a fonte pública não expõe ementa e conteúdo programático completos;
  - todas as capturas foram produzidas a partir de links públicos temporários gerados no ambiente ativo, preservando o estado oficial publicado.

## Snapshot Canônico de Regressão SIGAA - 2026-05-05

- Referência oficial para planejamento de testes e comparação de cobertura institucional:
  - `ementas-api/src/tests/fixtures/sigaa/full-catalog-results.2026-05-05.prd-final.json`
- Critério de uso:
  - todo ajuste de parser que afete cobertura deve comparar resultados contra este snapshot canônico;
  - snapshots auxiliares (`sample`, `rich-sample` e `final`) podem apoiar diagnóstico, mas não substituem a referência oficial.

## Assinatura de Template SIGAA (Procedimento de Debug e Aceite)

### Objetivo

- Capturar o HTML final de detalhe (apos submit JSF) e gerar uma assinatura rastreavel do template publico por unidade.
- Usar essas assinaturas como regressao de parser offline, sem dependencia de rede durante os testes.

### Procedimento Padrao

1. Habilitar instrumentacao de detalhe no backend:
  - `CRAWLER_SIGAA_DEBUG_DETAIL=1`
  - `CRAWLER_SIGAA_DEBUG_DIR=tmp/sigaa-detail-debug-<unidade>`
  - `CRAWLER_SIGAA_DEBUG_CODES` opcional para limitar codigos.
2. Executar importacao/reconciliacao SIGAA da unidade alvo:
  - exemplo: `npm run sigaa:reconcile -- --sourceType=department --sourceId=1114 --academicLevel=graduacao`
3. Coletar artefatos gerados (`.html` e `.json`) e versionar amostra representativa em:
  - `ementas-api/src/tests/fixtures/sigaa/detail-signatures`
4. Atualizar o manifesto de regressao:
  - `ementas-api/src/tests/fixtures/sigaa/detail-signatures/manifest.json`
5. Executar regressao automatizada do parser:
  - `CrawlerSigaaDetailSignatureRegression.spec.ts`

### Criterios de Aceite

- A amostra deve cobrir, no minimo, 3 unidades publicas relevantes (ex.: DCC 1114, DCI 2440, PGCOMP 1820).
- Cada unidade deve possuir amostra minima de 4 assinaturas de detalhe.
- O parser nao pode vazar rotulos estruturais como valor de pre-requisito (ex.: `Co-Requisitos:`).
- O teste de fallback multi-payload JSF deve permanecer verde, garantindo robustez quando ha mais de um `onclick` valido por linha.


