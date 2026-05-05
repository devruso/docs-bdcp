# Secao de Monografia - Validacao do Crawler SIGAA Publico

## Problema

Durante a evolucao do BDCP, a importacao de componentes via SIGAA publico apresentava risco de falso positivo: tokens tecnicos do HTML (CSS/JSF/scripts) podiam ser interpretados como codigo de disciplina quando a pagina nao possuia linhas curriculares reais.

## Justificativa Tecnica

Esse comportamento compromete qualidade de dados, rastreabilidade e confiabilidade academica da base institucional. Em um sistema para gestao formal de ementas e conteudos programaticos, importar dados inexistentes e mais grave que nao importar em um caso ambiguo.

## Decisao de Projeto

Foi adotada estrategia fail-safe no parser SIGAA:
- considerar apenas linhas tabulares com pelo menos duas celulas (`td`);
- extrair codigo curricular apenas nas celulas iniciais da linha, evitando ruido de layout;
- deduplicar por codigo;
- normalizar departamento a partir de celulas de contexto ou cabecalho da pagina;
- manter fallback de URLs por tipo de fonte (`department`/`program`) para ampliar compatibilidade sem alterar contrato da API.

## Evidencias Implementadas

- Ajuste de parser e fallback: `api-bdcp/src/services/CrawlerService.ts`.
- Regressao automatizada:
  - `api-bdcp/src/tests/CrawlerSigaaParser.spec.ts`;
  - fixture real sem componentes (`source-department-1876851.html`) garantindo rejeicao de falso positivo.
- Resultado de teste:
  - backend crawler/share: suites verdes (`6/6` no recorte executado);
  - frontend detalhe da disciplina: `8/8` cenarios verdes apos estabilizacao.

## Impacto Academico e Pratico

- Reduz risco de poluicao da base oficial por importacoes espurias.
- Reforca principio de fonte canonica: conteudo programatico oficial permanece governado no BDCP.
- Melhora auditabilidade da etapa de importacao, permitindo demonstrar criterios de qualidade para banca do TCC.

## Limitacoes e Proximos Passos

- Acuracia positiva em maior escala com paginas 100% reais de grade curricular do SIGAA exige completar descoberta de IDs/fluxo JSF que exponham tabela curricular completa sem autenticacao.
- Proximo ciclo recomendado:
  1. automatizar coleta com sessao JSF para DCC/DCI/PGCOMP;
  2. gerar verdade-terreno por amostra manual estratificada;
  3. recalcular acuracia por campo em lote e publicar no plano de testes.
