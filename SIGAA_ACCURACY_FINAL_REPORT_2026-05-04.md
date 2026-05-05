# Relatorio Final de Acuracia - Crawler SIGAA Publico

Data de consolidacao: 2026-05-04  
Projeto: BDCP (TCC)  
Objetivo: apresentar evidencias reais de descoberta de fonte, acuracia por campo e prontidao para banca.

## 1) Escopo e Metodo

- Fluxo de coleta: sessao JSF publica com submissao de formulario (`javax.faces.ViewState`) em `busca_componentes.jsf`.
- Fontes reais validadas:
  - DCC/IC (`unidade=1114`, nivel `G`)
  - DCI/IC (`unidade=2440`, nivel `G`)
  - PGCOMP/IC (`unidade=1820`, nivel `S`, mapeado para `mestrado` no parser)
- Verdade-terreno: amostra manual estratificada com 15 disciplinas (5 por fonte).
- Motor de avaliacao: script reprodutivel em `api-bdcp/src/scripts/sigaa-accuracy-report.ts`.

## 2) Descoberta Concluida (IDs/Fonte)

| Fonte | Source Type | Parametro de busca | Nivel | Evidencia |
|---|---|---|---|---|
| Departamento de Ciencia da Computacao (DCC/IC) | department | `form:unidades=1114` | `G` | 133 itens extraidos |
| Departamento de Computacao Interdisciplinar (DCI/IC) | department | `form:unidades=2440` | `G` | 32 itens extraidos |
| Programa de Pos-Graduacao em Ciencia da Computacao (PGCOMP/IC) | program | `form:unidades=1820` | `S` | 70 itens extraidos |

## 3) Resultado de Acuracia (Lote Positivo)

Base medida: 15 amostras estratificadas (5 DCC, 5 DCI, 5 PGCOMP).

| Campo | Acertos | Total | Acuracia |
|---|---:|---:|---:|
| code | 15 | 15 | 100% |
| name | 15 | 15 | 100% |
| department | 15 | 15 | 100% |
| academicLevel | 15 | 15 | 100% |

### Resumo visual (barra)

| Campo | Visual |
|---|---|
| code | `██████████ 100%` |
| name | `██████████ 100%` |
| department | `██████████ 100%` |
| academicLevel | `██████████ 100%` |

## 4) Ajustes Tecnicos que Viabilizaram o Resultado

- Parser endurecido para ignorar ruído de layout/JSF e manter foco em linhas tabulares reais.
- Suporte a codigo com prefixo de programa, ex.: `PGCOMP/IC0032` -> codigo normalizado `IC0032`.
- Refino de heuristica de departamento para evitar falso positivo em termos como `PROGRAMACAO`.
- Regressao automatizada adicionada para os cenarios acima.

## 5) Evidencias Reproduziveis

- Script de medicao: `api-bdcp/src/scripts/sigaa-accuracy-report.ts`
- Verdade-terreno estratificada: `api-bdcp/src/tests/fixtures/sigaa/manual-ground-truth-stratified.json`
- Resultado consolidado bruto: `api-bdcp/src/tests/fixtures/sigaa/accuracy-results.json`
- Fixtures reais de entrada:
  - `api-bdcp/src/tests/fixtures/sigaa/source-busca-componentes-dcc-1114.html`
  - `api-bdcp/src/tests/fixtures/sigaa/source-busca-componentes-dci-2440.html`
  - `api-bdcp/src/tests/fixtures/sigaa/source-busca-componentes-pgcomp-1820.html`

## 6) Leitura para Banca

- Confiabilidade observada no escopo medido: alta, com 100% nos campos avaliados.
- Risco residual: baixo para os cenarios cobertos; medio para unidades/fontes ainda nao estratificadas fora do recorte de computacao.
- Recomendacao de continuidade:
  1. ampliar amostra estratificada para outros institutos e niveis;
  2. incluir validacao de mudancas de layout do SIGAA em monitoramento periodico;
  3. manter conteudo programatico como fonte canonica no BDCP para publicacao oficial.
