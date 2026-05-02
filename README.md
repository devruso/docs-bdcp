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
- Gestao de usuarios atualizada com criacao direta de professor dentro do app (convite gerado e consumido no mesmo fluxo), sem necessidade de abrir pagina externa para concluir cadastro.
