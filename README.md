# SUCELSO TECH — Sistema Inteligente de Horários Escolares

Projeto Full Stack acadêmico para organizar disponibilidades docentes e horários escolares, conforme a situação de aprendizagem da Universidade SENAI CIMATEC.

## Objetivo

Centralizar cadastros acadêmicos, receber a disponibilidade de professores, gerar propostas de horários sem conflitos e permitir que a coordenação revise e publique a grade oficial.

## Perfis e regras principais

- **Aluno:** consulta os horários da própria turma; não altera dados.
- **Professor:** consulta sua grade e gerencia sua disponibilidade; não altera a grade oficial.
- **Coordenador:** administra cadastros, gera e ajusta propostas, acompanha conflitos e publica versões oficiais.
- A autorização deve ser validada no backend em toda operação protegida, não apenas escondendo controles na interface.
- Alterar a disponibilidade após a publicação não pode modificar automaticamente uma grade publicada.

## Escopo funcional

1. Autenticação por e-mail e senha com Supabase Auth e controle dos três perfis.
2. Cadastros de alunos, professores, disciplinas, turmas, salas, turnos e períodos letivos.
3. Registro e edição da disponibilidade docente durante o período de coleta.
4. Geração de propostas que respeitem disponibilidade, carga semanal, turnos e conflitos de professor, sala e turma; problemas e aulas não alocadas devem ser apresentados à coordenação.
5. Consulta da grade por aluno, professor e coordenador, incluindo dia, intervalo, sala, docente e disciplina.
6. Fluxo de versões em rascunho, revisão e publicada, com histórico de alterações e publicação exclusiva pela coordenação.

## Arquitetura solicitada

- **Frontend:** HTML/CSS/JavaScript ou React, publicado na Vercel.
- **Backend:** Node.js + Express, API REST/JSON publicada no Render.
- **Banco:** PostgreSQL no Supabase.
- **Autenticação:** Supabase Auth.
- Segredos ficam em variáveis de ambiente; chaves administrativas do Supabase nunca podem ir para o frontend.

## Plano de desenvolvimento por features

- [ ] **F0 — Planejamento:** requisitos, entidades, regras de negócio, casos de uso e divisão de trabalho.
- [ ] **F1 — Fundação:** estrutura frontend/backend, configuração local, variáveis de ambiente e integração inicial com Supabase.
- [ ] **F2 — Dados e autenticação:** schema/migrations, dados fictícios e autorização por perfil no backend.
- [ ] **F3 — Cadastros acadêmicos:** operações administrativas e relacionamentos entre as entidades.
- [ ] **F4 — Disponibilidades:** registro e atualização pelo próprio professor, com regras para períodos publicados.
- [ ] **F5 — Grade e algoritmo:** geração de propostas, detecção de conflitos e indicação de aulas não alocadas.
- [ ] **F6 — Revisão e publicação:** edição coordenada, versões, publicação oficial e trilha de auditoria.
- [ ] **F7 — Consultas e interface:** login e visões responsivas específicas para cada perfil.
- [ ] **F8 — Entrega:** cenário demonstrativo, documentação/DER, validações funcionais e de segurança e deploy Vercel/Render.

## Cenário mínimo de demonstração

O sistema deve suportar pelo menos 3 turmas, 6 professores, 8 disciplinas e 5 salas. A demonstração deve incluir um professor disponível apenas em determinados dias, geração e revisão da grade pela coordenação, publicação, consulta pelo aluno sem permissão de edição e mudança de disponibilidade sem alteração automática da grade publicada.

## Entregáveis

- Aplicação publicada e integrada de ponta a ponta.
- Repositório com frontend, backend, SQL, README, DER e regras de negócio.
- Autenticação e três perfis com permissões aplicadas no servidor.
- Algoritmo de geração e relatório de conflitos.
- Evidências de validação e apresentação técnica pela dupla.

## Diferenciais possíveis

Exportação em PDF, notificações de alterações, indicadores de ocupação das salas e minimização de janelas entre aulas, sempre preservando as restrições obrigatórias.

## Desenvolvimento

As funcionalidades serão implementadas em branches de feature e integradas em `develop` em etapas pequenas. Consulte o histórico do Git para acompanhar cada etapa.
