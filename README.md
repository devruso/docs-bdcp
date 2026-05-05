# General Documentation
## Database System of Syllabus of the Subjects of the Federal University of Bahia
### In this repository you found all documentation of this project.

- [Requirements Documentation](REQUIREMENTS.md)
- [Architectural Documentation](ARCHITECTURE.md)
- [Implementation Documentation](IMPLEMENTATION.md)

- [Specifications](./assets/especification.pdf)
- [Clickup Manager](https://app.clickup.com/31043046/settings/team/31043046/project)
- [Figma Prototype](https://www.figma.com/file/4sI0IP4YRbJTMOepIR8XaY/Prot%C3%B3tipo?node-id=0%3A1)

## Incrementos Backend Recentes

- Importacao de documento para rascunho via endpoint autenticado de preview multipart em api-bdcp, com suporte inicial a PDF e DOCX.
- Extracao heuristica de codigo, nome, departamento, semestre, ementa, objetivos, conteudo programatico, metodologia, avaliacao, bibliografia, pre-requisitos e cargas para revisao humana antes do salvamento.
- Exportacao PDF atualizada para incluir metadados de aprovacao formal da disciplina quando existirem no historico.
- Pre-requisitos agora seguem classificacao operacional no frontend (existente, pendente e nao se aplica), com selecao automatica por disciplinas publicadas e rascunhos; no backend, codigos sao normalizados e autorreferencia continua bloqueada.
- Gestao de usuarios atualizada com endpoint administrativo de criacao direta de professor, com senha provisoria segura gerada no backend e envio opcional de credenciais via SMTP institucional.
- Governanca de perfis evoluida com papel `SUPER_ADMIN` formal (compativel com `admin` atual), incluindo endpoint de alteracao de role protegido por super administracao.
- Publicacao oficial com politica de assinatura obrigatoria: aprovacao de rascunho agora valida assinatura configurada no perfil, sem incluir assinatura nos artefatos DOCX/PDF exportados.
- Publicacao global irrestrita removida para consulta de disciplinas e rascunhos; acesso publico agora ocorre por compartilhamento temporario com token, expiracao e revogacao.
- Painel de disciplinas evoluido com governanca operacional de compartilhamento: listagem de links publicos ativos por disciplina, revogacao unitaria e revogacao em massa no proprio app.
- Crawler evoluido para SIGAA publico (departamento/programa) com classificacao por nivel academico (`graduacao`, `mestrado`, `doutorado`), preservando o conteudo programatico como fonte canonica do BDCP.
- Parser do crawler SIGAA refinado com regressao offline baseada em HTML real: eliminacao de falsos positivos por ruído tecnico (tokens CSS/JSF) e fallback de URLs por tipo de fonte (`department`/`program`).
- Descoberta de fonte/IDs via fluxo JSF concluida para DCC (`1114`), DCI (`2440`) e PGCOMP (`1820`), com matriz de casos reais e relatorio final de acuracia para banca.
- Resumo executivo para slides da defesa, com tabela consolidada e grafico de acuracia: `SIGAA_ACCURACY_SLIDES_2026-05-05.md`.
- Painel de usuarios evoluido para governanca de perfis por `SUPER_ADMIN`, com promocao/rebaixamento de roles diretamente na interface de administracao.
