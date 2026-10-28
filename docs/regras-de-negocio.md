# Regras de negócio — SUCELSO TECH

Este documento registra as regras funcionais da atividade para orientar a implementação. Ele não define o schema do banco, que está sendo preparado pelo outro integrante da dupla.

## 1. Perfis e autorização

| Ação | Aluno | Professor | Coordenador |
| --- | --- | --- | --- |
| Entrar e consultar dados acadêmicos | Sim | Sim | Sim |
| Consultar horário | Da própria turma | Da própria grade/turmas | Todas as grades |
| Registrar/editar disponibilidade | Não | Própria disponibilidade | Administrar conforme período e necessidade |
| Administrar cadastros acadêmicos | Não | Não | Sim |
| Gerar proposta de horário | Não | Não | Sim |
| Editar, aprovar ou publicar horário oficial | Não | Não | Sim |
| Consultar conflitos e relatórios gerais | Não | Não | Sim |

- A identidade e o perfil devem ser verificados no servidor em cada operação protegida; a interface não é uma barreira de segurança.
- Um professor só pode consultar/alterar sua própria disponibilidade e consultar horários associados a ele.
- Um aluno só pode consultar o horário da turma à qual está vinculado. Nenhuma operação de escrita na grade é permitida para aluno ou professor.
- O coordenador é o único perfil que altera cadastros acadêmicos e versões oficiais da grade.
- Tentativas sem autenticação devem receber resposta HTTP 401; usuário autenticado sem permissão deve receber HTTP 403.

## 2. Disponibilidade docente

- A disponibilidade representa intervalos em que o professor pode lecionar em um dia da semana.
- O professor registra e altera apenas os próprios intervalos durante o período de coleta definido pela coordenação.
- Cada intervalo deve ter dia válido, início anterior ao fim e estar dentro dos turnos letivos configurados.
- Intervalos sobrepostos da mesma pessoa devem ser rejeitados ou consolidados, sem criar disponibilidade ambígua.
- A geração de uma proposta só pode alocar aulas em intervalos cobertos pela disponibilidade do docente.
- Após a publicação de uma grade, uma mudança de disponibilidade não atualiza nem invalida automaticamente os horários publicados. A solicitação deve ser encaminhada ao coordenador para avaliação.

## 3. Restrições obrigatórias da grade

Uma proposta válida precisa obedecer simultaneamente a todas as restrições abaixo:

1. **Professor:** não pode ministrar duas aulas em intervalos sobrepostos.
2. **Sala:** não pode receber mais de uma turma em intervalos sobrepostos.
3. **Turma:** não pode cursar duas disciplinas em intervalos sobrepostos.
4. **Disponibilidade:** cada aula deve ocorrer quando o docente responsável estiver disponível.
5. **Carga horária:** cada vínculo turma-disciplina-docente deve cumprir sua carga semanal configurada.
6. **Turno/calendário:** a aula precisa estar dentro dos dias, turnos e períodos letivos ativos; intervalos institucionais (por exemplo, recreio) não recebem aulas.
7. **Vínculo acadêmico:** a alocação deve usar uma disciplina associada à turma e ao docente corretos.

Conflitos de professor, sala ou turma são duros: o sistema não deve gerar/publicar uma grade que os contenha. Quando uma combinação não puder ser alocada, o sistema deve reportar a aula não alocada e explicar a restrição impeditiva para que a coordenação possa revisar os dados ou a proposta.

## 4. Propostas, revisão e publicação

- A geração cria uma proposta em estado **rascunho** e não altera a grade oficial publicada.
- A coordenação pode revisar e ajustar uma proposta; ajustes manuais também devem respeitar as restrições obrigatórias.
- Estados mínimos da versão: **rascunho**, **em revisão** e **publicada**.
- Somente uma versão publicada é oficial para consulta por alunos e professores.
- A publicação é exclusiva do coordenador e deve ocorrer apenas depois da validação de conflitos e cargas horárias.
- Uma nova publicação deve preservar as versões anteriores e seu histórico, em vez de sobrescrevê-las sem rastreabilidade.
- O histórico registra quem realizou a ação, quando ocorreu, qual operação foi feita e quais valores foram alterados.

## 5. Consultas

- A grade exibida deve informar, no mínimo, turma, disciplina, docente, sala, dia da semana, horário de início e fim e situação da aula.
- Alunos e professores veem apenas a versão publicada.
- Coordenadores podem consultar versões em rascunho/revisão e todas as grades.
- A consulta de salas, disciplinas e turmas respeita as permissões por perfil definidas na matriz acima.

## 6. Cenário de validação da atividade

O cenário demonstrativo deve conter pelo menos 3 turmas, 6 professores, 8 disciplinas e 5 salas. Deve demonstrar que:

1. um professor registra disponibilidade somente em determinados dias (o exemplo da atividade usa segunda e quarta);
2. o coordenador gera uma proposta que respeita essa disponibilidade;
3. o sistema identifica conflitos de professor, sala e turma e informa aulas não alocadas;
4. o coordenador ajusta a proposta e publica a versão oficial;
5. o aluno consulta a própria turma e não consegue alterar a grade, inclusive por requisição direta à API;
6. uma mudança posterior de disponibilidade docente não modifica automaticamente a grade publicada.

## 7. Decisões que precisam ser configuráveis

Os horários de aula, duração de cada slot, turnos, dias letivos, pausas, período de coleta e eventual limite de carga docente não estão fixados no enunciado. Devem ser definidos/configuráveis pela coordenação; não devem ser codificados como valores arbitrários no algoritmo.

## 8. Fora do obrigatório

Exportação PDF, notificações, indicadores de ocupação e minimização de janelas são diferenciais. Podem ser acrescentados depois que as permissões, restrições e publicação obrigatórias estiverem funcionando.
