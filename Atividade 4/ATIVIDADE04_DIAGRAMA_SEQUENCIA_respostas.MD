# Atividade 4: Diagramas de sequência

**Projeto de referência:** VazaApp  
**Caso de uso:** UC-017: Gerar sugestão personalizada

## Exercício 16

O caso de uso descreve o objetivo do ator e os caminhos para alcançá-lo. O diagrama de sequência mostra como os participantes trocam mensagens em um desses cenários.

Ele não substitui o caso de uso, pois não reúne necessariamente todas as regras, condições, alternativas e exceções.

## Exercício 17

| Papel | Participante e responsabilidade |
|---|---|
| Ator | Cliente: solicita a transferência e recebe o resultado. |
| Interface | TelaTransferencia: recebe os dados e apresenta mensagens. |
| Aplicação | TransferenciaService: coordena as etapas da operação. |
| Domínio | Conta e Transferencia: verificam saldo e regras da movimentação. |
| Persistência | ContaRepository: consulta as contas e grava débito e crédito juntos, evitando uma transferência incompleta. |

## Exercício 18

O professor solicita e confirma a reserva. A tela recebe os dados, o controller encaminha a solicitação, o service coordena o fluxo e o repository consulta e grava a reserva.

![Diagrama do exercício 18](Diagramas%20Sequencias/exercicio18.svg)

## Exercício 19

A RN05 impede reservas com períodos conflitantes. O `alt` separa os caminhos de sala disponível e indisponível. A confirmação também verifica o conflito na gravação, evitando duas reservas simultâneas para a mesma sala.

![Diagrama do exercício 19](Diagramas%20Sequencias/exercicio19.svg)

## Exercício 20

O `loop` verifica a disponibilidade, adiciona o item aceito e calcula seu subtotal para cada produto. Assim, a sequência atende qualquer quantidade de itens sem repetir manualmente as mensagens.

![Diagrama do exercício 20](Diagramas%20Sequencias/exercicio20.svg)

## Exercício 21

A TelaPedido concentra regras, gravação e envio de email. Isso dificulta os testes e faz mudanças no negócio afetarem a interface.

| Participante | Responsabilidade |
|---|---|
| TelaPedido | Receber dados e mostrar o resultado. |
| PedidoService | Coordenar a operação. |
| DominioPedido | Validar cliente e estoque, aplicar desconto e calcular o total. |
| PedidoRepository | Gravar pedido e atualização do estoque juntos, verificando conflitos. |
| ServicoEmail | Enviar a confirmação após a gravação. |

Se a gravação falhar, o pedido não é confirmado e o email não é enviado.

![Diagrama do exercício 21](Diagramas%20Sequencias/exercicio21.svg)

## Exercício 22

Antes de manter `bloquearCliente()`, eu verificaria sua origem e seu efeito com os responsáveis.

| Possibilidade | Encaminhamento |
|---|---|
| Erro no diagrama | Remover o bloqueio sem justificativa. |
| Requisito incompleto | Registrar o comportamento validado no requisito e no caso de uso. |
| Regra ausente | Documentar as condições do bloqueio e relacioná-las ao requisito. |
| Decisão técnica legítima | Justificar e renomear a mensagem se for apenas uma trava temporária de concorrência. |

Uma decisão técnica não autoriza criar um bloqueio de negócio. Se o comportamento afetar o cliente, RN, RF e UC precisam documentá-lo.

## Exercício 23

### Regras e requisitos

| Regra de negócio | Requisito funcional |
|---|---|
| RN01: agendar somente em horário disponível. | RF01: apresentar horários por especialidade, profissional e data. |
| RN02: impedir duas consultas ativas do profissional no mesmo horário. | RF02: confirmar a consulta sem conflito e informar o resultado. |
| RN03: permitir cancelamento com antecedência de pelo menos 24 horas, incluindo exatamente 24 horas. | RF03: cancelar a consulta após verificar o prazo. |
| RN04: preservar consultas e cancelamentos no histórico. | RF04: registrar cada operação com identificação e data/hora, sem apagar a consulta cancelada. |

| RNF proposto | Critério |
|---|---|
| RNF01: integridade | Em confirmações simultâneas da mesma vaga, aceitar no máximo uma, sem registros parciais. |
| RNF02: acesso | Permitir que o paciente cancele somente suas consultas. |
| RNF03: desempenho | Responder 95% das consultas de disponibilidade em até 2 segundos, com 20 usuários simultâneos e 1.000 consultas cadastradas. |

Esses RNFs são propostas para validação. Não foram executados testes de implementação.

### Ator e casos de uso

O ator é o paciente. Os casos de uso são UC01: Consultar disponibilidade, UC02: Agendar consulta e UC03: Cancelar consulta. O profissional é uma entidade do domínio, pois o enunciado não descreve sua interação com o sistema.

### UC02: Agendar consulta

| Campo | Descrição |
|---|---|
| Objetivo | Confirmar a consulta sem conflito na agenda. |
| Ator | Paciente. |
| Pré-condições | Paciente identificado; especialidade, profissional e agenda cadastrados e compatíveis. |
| Sucesso | Consulta registrada e vaga ocupada. |
| Falha | Nenhuma consulta confirmada nem registro parcial. |

| Passo | Fluxo principal |
|---|---|
| 1 | O paciente solicita o agendamento. |
| 2 | O sistema solicita especialidade, profissional e data. |
| 3 | O paciente informa os filtros. |
| 4 | O sistema apresenta os horários disponíveis. |
| 5 | O paciente escolhe um horário e confirma. |
| 6 | O sistema revalida a vaga e registra consulta e operação de forma atômica. |
| 7 | O sistema confirma o agendamento. |

| Desvio | Comportamento |
|---|---|
| A1, passo 4: sem horários | Alterar filtros e voltar ao passo 3 ou encerrar. |
| A2, passo 6: vaga ocupada | Não agendar; atualizar os horários e voltar ao passo 4. |
| A3, antes da confirmação | Encerrar sem criar consulta se o paciente desistir. |
| E1, passos 3 ou 5: dados inválidos | Informar o problema e solicitar correção no mesmo passo. |
| E2, passo 6: falha na gravação | Desfazer toda a operação e encerrar sem confirmação. |

Cada horário representa uma vaga. A duração das consultas e o funcionamento da clínica ainda precisam ser definidos.

### Rastreabilidade

| Regra ou decisão | Requisito | Caso de uso e aplicação |
|---|---|---|
| RN01 | RF01, RF02, RNF03 | UC01 e UC02: consultar e revalidar disponibilidade. |
| RN02 | RF02, RNF01 | UC02: garantir ocupação exclusiva da vaga. |
| RN03 | RF03 | UC03: aceitar cancelamento com antecedência maior ou igual a 24 horas. |
| RN04 | RF04, RNF01 | UC02 e UC03: registrar operações e preservar o histórico. |
| Segurança proposta | RNF02 | UC03: conferir se a consulta pertence ao paciente. |

### Checklist e correções

| Conferência | Resultado |
|---|---|
| Objetivo, ator e condições | Definidos no UC02. |
| Alternativas e exceções | Informam origem, retorno ou encerramento. |
| Concorrência | A confirmação garante exclusividade na gravação; a consulta inicial não basta. |
| Cancelamento | Exatamente 24 horas é permitido e o registro é preservado. |
| Coerência do diagrama | Representa o agendamento. O cancelamento pertence ao UC03. |

O UC02 acima já incorpora as correções de concorrência, retorno dos desvios e tratamento de falhas.

### Participantes e diagrama

| Participante | Responsabilidade |
|---|---|
| Paciente | Escolher os dados e confirmar. |
| TelaAgendamento | Receber dados e mostrar opções e resultados. |
| AgendamentoController | Encaminhar as solicitações. |
| AgendamentoService | Coordenar o fluxo e aplicar as regras de disponibilidade. |
| ConsultaRepository | Consultar a agenda e gravar com exclusividade e transação. |

O `alt` distingue horário disponível e indisponível. A decisão de agendar cabe ao serviço; o repositório garante a restrição durante a gravação.

![Diagrama do exercício 23](Diagramas%20Sequencias/exercicio23.svg)

## Exercício 24

Escolhi o UC-017: Gerar sugestão personalizada, do VazaApp. A análise, a revisão e o diagrama estão no [exercício 24](exercicio24.md).

## Questão final

O diagrama também revela problemas e decisões que o texto pode deixar em aberto.

Nos exercícios 18 e 19, duas pessoas podem tentar reservar a mesma sala. Isso mostra que o conflito precisa ser impedido na gravação.

No exercício 21, a concentração de tarefas na TelaPedido revela a necessidade de separar responsabilidades e enviar o email somente após salvar o pedido.
