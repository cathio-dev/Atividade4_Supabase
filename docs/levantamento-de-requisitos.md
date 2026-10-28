# Fase 0 — Levantamento de requisitos

**Produto:** SUCELSO TECH — Sistema Inteligente de Horários Escolares  
**Fonte principal:** Situação de Aprendizagem SUCELSO TECH — Horários  
**Escopo deste documento:** registrar o que o sistema precisa fazer e como validar o resultado. O schema físico do PostgreSQL/Supabase fica para a etapa de banco.

## 1. Objetivo do produto

Disponibilizar um sistema web que centralize cadastros acadêmicos e disponibilidades docentes, gere propostas de horário sem conflitos e permita à coordenação revisar e publicar a grade oficial. Alunos e professores consultam as informações de acordo com seu perfil.

## 2. Atores

- **Aluno:** consulta a grade publicada da própria turma.
- **Professor:** consulta a própria grade/turmas e informa a própria disponibilidade.
- **Coordenador:** administra dados acadêmicos, acompanha conflitos, gera e ajusta propostas e publica a grade.
- **Serviço de autenticação:** valida a identidade e fornece a sessão usada pela API.

## 3. Requisitos funcionais

Prioridade **Obrigatório** indica requisito explícito para aprovação na atividade. **Complementar** indica suporte necessário ao fluxo ou requisito que pode ser detalhado com a dupla.

| ID | Prioridade | Requisito | Critério verificável |
| --- | --- | --- | --- |
| RF-01 | Obrigatório | Autenticar usuários por e-mail e senha. | Usuário válido inicia sessão; credenciais inválidas não dão acesso. |
| RF-02 | Obrigatório | Associar cada usuário autenticado a um perfil: aluno, professor ou coordenador. | A API reconhece o perfil do usuário autenticado em cada requisição protegida. |
| RF-03 | Obrigatório | Aplicar autorização no backend por perfil e por registro. | Aluno não altera grade; professor não altera grade e não acessa disponibilidade alheia; coordenador tem as operações administrativas. |
| RF-04 | Obrigatório | Permitir ao coordenador gerenciar alunos, professores, disciplinas, turmas, salas, turnos e períodos letivos. | Operações de cadastro/edição são restritas ao coordenador e preservam os vínculos acadêmicos. |
| RF-05 | Obrigatório | Permitir ao professor registrar e editar a própria disponibilidade durante a coleta. | Professor só escreve em seus próprios intervalos; entrada inválida é recusada com mensagem adequada. |
| RF-06 | Obrigatório | Encaminhar alterações de disponibilidade após publicação para avaliação da coordenação. | A alteração não muda uma grade já publicada automaticamente. |
| RF-07 | Obrigatório | Gerar uma proposta de grade a pedido do coordenador. | Proposta respeita disponibilidade, carga semanal, turnos/pausas e ausência de conflitos de turma, professor e sala. |
| RF-08 | Obrigatório | Informar conflitos e aulas que não puderam ser alocadas. | Resultado identifica as alocações problemáticas e uma causa compreensível. |
| RF-09 | Obrigatório | Permitir à coordenação revisar, editar e publicar versões da grade. | Somente coordenador publica; versão publicada passa a ser a oficial, com histórico preservado. |
| RF-10 | Obrigatório | Expor consulta de horários conforme o perfil. | Aluno vê a turma associada; professor vê sua grade; coordenador consulta todas as grades e versões. |
| RF-11 | Obrigatório | Exibir turma, disciplina, professor, sala, dia, início, fim e situação da aula. | Cada aula visível contém todos esses dados. |
| RF-12 | Obrigatório | Manter estados de versão rascunho, em revisão e publicada. | Estados são persistidos; apenas publicada é mostrada como grade oficial aos alunos e professores. |
| RF-13 | Obrigatório | Registrar histórico das alterações oficiais. | Para cada operação relevante é possível identificar usuário, data, operação e valores alterados. |
| RF-14 | Obrigatório | Persistir os dados em PostgreSQL/Supabase e integrar frontend e API REST. | Dados sobrevivem a nova sessão e a aplicação publicada usa a API e o banco configurados. |
| RF-15 | Obrigatório | Disponibilizar endpoints REST previstos no enunciado. | Consultas, disponibilidades, geração, edição, publicação e conflitos têm operações HTTP documentadas e autorizadas. |
| RF-16 | Obrigatório | Preparar dados fictícios suficientes para a demonstração. | Há pelo menos 3 turmas, 6 professores, 8 disciplinas e 5 salas. |
| RF-17 | Complementar | Coordenador configura calendário, turnos, slots/intervalos e período de coleta. | Algoritmo usa os parâmetros acadêmicos configurados; valores não são suposições escondidas no código. |
| RF-18 | Complementar | Aluno, professor e coordenador consultam disciplinas, salas e turmas conforme a matriz de acesso. | Cada perfil obtém apenas as consultas previstas para seu papel. |

### Endpoints do enunciado

| Método | Caminho | Finalidade resumida |
| --- | --- | --- |
| GET | `/api/horarios/minha-turma` | Horário da turma do aluno autenticado |
| GET | `/api/horarios/professor` | Horário do professor autenticado |
| GET | `/api/disciplinas` | Consulta de disciplinas |
| GET | `/api/salas` | Consulta de salas |
| POST | `/api/disponibilidades` | Registrar disponibilidade |
| PUT | `/api/disponibilidades/:id` | Atualizar disponibilidade própria |
| POST | `/api/horarios/gerar` | Gerar proposta (coordenador) |
| PUT | `/api/horarios/:id` | Alterar alocação (coordenador) |
| POST | `/api/horarios/versoes/:id/publicar` | Publicar versão (coordenador) |
| GET | `/api/horarios/conflitos` | Consultar conflitos (coordenador) |

## 4. Requisitos não funcionais

| ID | Requisito | Critério/decisão inicial |
| --- | --- | --- |
| RNF-01 | Segurança | Validar sessão, papel e propriedade do registro no servidor; negar por padrão; não confiar em controles da interface. |
| RNF-02 | Proteção de credenciais | Segredos em variáveis de ambiente; chave administrativa do Supabase somente no backend. |
| RNF-03 | API consistente | Validar entradas e responder com códigos HTTP apropriados (por exemplo, 400 para entrada inválida, 401 sem autenticação, 403 sem autorização, 404 sem recurso e 409 para conflito de estado). |
| RNF-04 | Integridade | Persistir relações acadêmicas com chaves/validações no banco; não publicar versão com conflitos obrigatórios ou dados incompletos. |
| RNF-05 | Usabilidade | Interface responsiva e consultas com linguagem clara, especialmente para conflitos e aulas não alocadas. |
| RNF-06 | Rastreabilidade | Registrar operações relevantes com usuário e data; preservar versões publicadas. |
| RNF-07 | Implantação | Frontend acessível na Vercel e API acessível no Render, integrados ao Supabase, conforme a arquitetura exigida. |
| RNF-08 | Manutenibilidade | Separar interface, API, regras de domínio e acesso a dados; documentar configuração local e publicação no README. |
| RNF-09 | Compatibilidade de dados | Usar dias/horários e fuso de forma consistente; datas e regras do calendário devem ser configuráveis pela instituição. |

> Os requisitos de desempenho, disponibilidade e retenção não têm metas numéricas no enunciado. A dupla deve defini-las apenas se forem cobradas na demonstração; não inventar SLAs.

## 5. Casos de uso principais

### UC-01 — Consultar a grade como aluno

**Ator:** aluno autenticado. **Pré-condição:** conta associada a uma turma e existe versão publicada.

1. O aluno abre a consulta de horários.
2. A API identifica o aluno e obtém sua turma a partir do vínculo autenticado.
3. O sistema apresenta apenas a grade publicada dessa turma, com disciplina, professor, sala, dia e intervalo.
4. Se não houver versão publicada, o sistema informa que ainda não há grade oficial.

**Falha de autorização:** tentativa de consultar outra turma ou alterar a grade retorna acesso negado.

### UC-02 — Registrar disponibilidade como professor

**Ator:** professor autenticado. **Pré-condição:** período de coleta aberto.

1. O professor escolhe dia e intervalo disponível.
2. O sistema valida formato, início/fim, turnos e sobreposição com intervalos existentes.
3. A API grava o intervalo associado ao professor autenticado.
4. O sistema confirma o registro.

**Alternativa:** se a coleta estiver encerrada ou a disponibilidade for inválida, o sistema explica o motivo e não grava a alteração direta. Depois de publicada uma grade, eventual mudança é encaminhada para análise da coordenação e não reescreve a grade.

### UC-03 — Gerar e revisar proposta

**Ator:** coordenador autenticado. **Pré-condição:** dados acadêmicos e disponibilidades estão cadastrados.

1. O coordenador solicita geração para um período letivo.
2. O algoritmo procura alocações que satisfaçam as restrições obrigatórias.
3. O sistema cria uma versão em rascunho e lista conflitos residuais/aulas não alocadas com suas causas.
4. O coordenador revisa e ajusta alocações; cada ajuste é validado contra as mesmas restrições.
5. A proposta pode ser marcada em revisão.

### UC-04 — Publicar a grade oficial

**Ator:** coordenador autenticado. **Pré-condição:** proposta revisada, completa e sem conflitos obrigatórios.

1. O coordenador solicita publicação de uma versão.
2. O sistema revalida permissões, conflitos e estado da versão.
3. Se válida, publica a versão e registra responsável, data e alteração no histórico.
4. Alunos e professores passam a consultar a versão oficial.

**Alternativa:** conflitos ou estado inválido impedem a publicação; a API explica o que precisa ser corrigido.

## 6. Critérios de aceite do produto

- [ ] Existem contas/dados demonstrativos para os três perfis e os volumes mínimos do enunciado.
- [ ] As permissões são aplicadas também em chamadas diretas à API.
- [ ] O cenário de disponibilidade apenas segunda e quarta gera alocações somente nesses dias para aquele professor.
- [ ] Uma mesma pessoa, sala ou turma nunca aparece em duas aulas sobrepostas na proposta publicável.
- [ ] O sistema sinaliza aulas impossíveis de alocar e conflitos com uma explicação útil.
- [ ] Somente coordenador consegue ajustar e publicar uma versão oficial.
- [ ] Aluno consulta a própria turma e não consegue alterar a grade.
- [ ] Alterar disponibilidade depois da publicação não altera o horário publicado.
- [ ] Histórico permite identificar quem publicou/alterou uma versão e quando.
- [ ] Frontend, backend e Supabase funcionam juntos em URLs publicadas acessíveis.

## 7. Glossário conceitual de entidades

Este glossário descreve conceitos do domínio, não nomes definitivos de tabelas ou colunas. A modelagem relacional e suas chaves ficam com a etapa de banco. O [modelo conceitual editável](modelo-conceitual.drawio) usa blocos retangulares, conectores ortogonais e cardinalidades explícitas. A associação turma-disciplina é N:M conceitualmente e aparece resolvida por Oferta, que também vincula professor e carga semanal.

| Conceito | Significado e relações importantes |
| --- | --- |
| Usuário | Identidade de acesso, nome, e-mail e perfil. Pode estar associado ao registro acadêmico de aluno, professor ou coordenador. |
| Professor | Pessoa docente; tem matrícula, pode ter disponibilidades e lecionar disciplinas para turmas. |
| Aluno | Pessoa estudante; tem matrícula e vínculo com uma turma. |
| Coordenador | Usuário com permissão administrativa e responsabilidade por versões oficiais. Pode ser tratado como perfil de usuário, sem entidade acadêmica própria se não houver dados adicionais. |
| Disciplina | Componente curricular com nome/código e carga horária. Pode ser ofertado a uma turma por um professor. |
| Turma | Grupo de alunos associado a turno e período letivo; possui disciplinas ofertadas. |
| Sala | Espaço físico com identificação e capacidade, utilizado por aulas. |
| Turno | Janela/organização de funcionamento letivo, que limita horários possíveis. |
| Período letivo | Semestre ou intervalo acadêmico ao qual turmas e horários pertencem. |
| Oferta/Vínculo turma-disciplina | Associação da disciplina à turma e ao docente responsável, incluindo a carga semanal a cumprir. |
| Disponibilidade docente | Intervalo semanal em que um professor aceita lecionar, sujeito ao período de coleta. |
| Versão de horário | Conjunto versionado de alocações para um período, com estado rascunho, revisão ou publicada e responsável. |
| Aula/Alocação | Ocorrência de uma oferta em dia e intervalo, usando uma sala, pertencente a uma versão de horário. |
| Conflito | Diagnóstico de restrição violada ou de aula não alocada, apresentado à coordenação; pode ser resultado calculado em vez de entidade persistente. |
| Histórico de alteração | Registro de ator, data, operação e valores alterados em cadastros/versões. |

## 8. Pontos a confirmar com a dupla/docente

1. Duração dos slots e grade de horários permitidos em cada turno.
2. Dias letivos, pausas oficiais e exceções do calendário.
3. Quem abre/fecha o período de coleta de disponibilidade.
4. Se carga horária é armazenada como horas-aula ou quantidade de slots; o algoritmo precisa de uma unidade única.
5. Se a geração precisa otimizar alguma preferência além de satisfazer as restrições duras.
6. Se uma versão publicada pode ser substituída por outra sem período de transição e como as versões antigas aparecem.
7. Política de criação das contas e associação inicial de perfis (convite, cadastro coordenado ou seed demonstrativo).

## 9. Práticas recomendadas para esta etapa

- Dar identificador estável a cada requisito e registrar sua origem no enunciado, sua prioridade e uma forma objetiva de validação.
- Escrever requisitos de modo atômico: uma regra por item, sem termos vagos como “rápido”, “fácil” ou “inteligente” sem critério.
- Transformar fluxos críticos em casos de uso ou histórias com critérios de aceite e caminhos alternativos/negativos.
- Manter uma matriz perfil × ação e testar autorização no servidor, incluindo acesso a registros de outra pessoa; usar negação por padrão e privilégio mínimo.
- Separar regras obrigatórias de preferências de otimização. O algoritmo pode otimizar depois, mas jamais violar as restrições obrigatórias.
- Distinguir modelo conceitual (vocabulário/relacionamentos), modelo lógico e schema físico. Isso evita que o documento de requisitos dite detalhes prematuros do banco.
- Manter uma lista de decisões em aberto e registrar decisões com data e justificativa quando a dupla/professor confirmar as premissas.
- Descrever API com contrato (por exemplo, OpenAPI), cobrindo autenticação, autorização, entradas, respostas, erros e exemplos. O contrato ajuda frontend e backend a trabalhar em paralelo.
- Usar critérios de aceite e uma definição de pronto comum: implementado, revisado pela dupla, integrado, documentado e demonstrável no fluxo real.
- Tratar segurança como requisito desde o levantamento: papéis, propriedade dos dados, sessão, segredos, validação de entradas e trilha de auditoria.

## Referências consultadas

- [Scrum Guide oficial](https://scrumguides.org/scrum-guide.html) — objetivo de produto, transparência e definição de pronto.
- [OWASP ASVS](https://asvs.owasp.org/) — catálogo de requisitos para projetar e verificar controles de segurança de aplicações web.
- [OWASP Web Security Testing Guide — requisitos e planejamento](https://wstg.owasp.org/latest/3-The_OWASP_Testing_Framework/0-The_Web_Security_Testing_Framework/) — tópicos de identidade, autorização, dados, sessões e contratos de API a considerar desde o início.
- [OpenAPI — introdução a contratos de API](https://learn.openapis.org/introduction.html) — descrição legível por máquina e benefícios de consistência entre documentação e implementação.
