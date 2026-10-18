# Exercício 24

**Projeto de referência:** VazaApp  
**Caso de uso:** UC-017: Gerar sugestão personalizada

Escolhi esse caso por representar o objetivo principal do aplicativo: gerar sugestões personalizadas.

## Regras e requisitos

| Regras | Requisito e aplicação |
|---|---|
| RN-002 e RN-003 | RF-017, RNF-001/002: verificar sessão e propriedade. |
| RN-008 e RN-009 | RF-017: validar dados e estado, preservando a fotografia do pedido. |
| RN-010 e RN-011 | RF-017: selecionar modelo ativo e compatível, sem inventar fatos. |
| RN-012 | RF-017 e RNF-006: confirmar sugestão, vínculo, histórico e estado juntos. |
| RN-024 | RF-017 e RNF-006: repetição sem duplicação. |

O RNF-003 exige mensagens claras; o RNF-007, registro técnico de falhas.

## Caso de uso corrigido

| Item | Definição |
|---|---|
| Objetivo e ator | Usuário obtém sugestão personalizada. |
| Gatilho e entradas | Solicitar geração com identificador do pedido e chave da operação. |
| Pré-condições | Conta ativa e pedido existente. |
| Pós-condição | Resultado consistente, sem alteração parcial. |

### Fluxo principal

| Passo | Comportamento |
|---|---|
| P1 | Usuário solicita geração. |
| P2 | Sistema verifica sessão e propriedade antes do conteúdo. |
| P3 | Recupera fotografia e procura resultado pela chave. |
| P4 | Valida situação, categoria, destinatário e estado. Geração direta exige rascunho; alternativas usam UC-018. |
| P5 | Consulta modelos ativos compatíveis. |
| P6 | Seleciona pelas tags, desempata pelo menor identificador e exclui modelos usados no UC-018. |
| P7 | Personaliza parâmetros permitidos, sem inventar dados. |
| P8 | Confirma sugestão, vínculo, histórico e estado `com_sugestao` juntos, verificando chave, propriedade e conflitos. |
| P9 | Exibe sugestão após confirmação. |

### Alternativas e exceções

| Caminho | Tratamento |
|---|---|
| A1, P6: nenhum modelo | Informa indisponibilidade e encerra sem alterações. |
| A2, P3/P8: chave confirmada | Recupera resultado existente e segue para P9. |
| E1, P4: dados/estado inválidos | Informa pendências e encerra. Correção: UC-015; alternativas: UC-018. |
| E2, P2: acesso inválido | Nega acesso sem revelar conteúdo. |
| E3, P8: falha/conflito | Desfaz operação, preserva registros e mantém chave na tentativa sem confirmação. |

## Checklist e revisão

| Verificação | Correção incorporada |
|---|---|
| Acesso | Autorização antes do conteúdo. |
| Personalização | Contexto opcional, sem fatos inventados. |
| Caminhos | Origem e desfecho definidos. |
| Consistência | Confirmação conjunta, repetição sem duplicação e falhas sem resultado parcial. |

A revisão é documental; CT-017.1/2/3 permanecem propostos.

## Diagrama de sequência

![UC-017: Gerar sugestão personalizada](Diagramas%20Sequencias/UC-017.svg)

## Responsabilidades

| Participante ou elemento | Justificativa |
|---|---|
| Usuário | Solicita e recebe: P1/P9. |
| Interface de sugestões | Apresenta interação e mensagens: RF-017, RNF-003. |
| Serviço de geração | Coordena execução: P2 a P9, RN-002/003/024. |
| Política de geração | Valida, seleciona e personaliza: P4/P6/P7, RN-008/010/011. |
| Catálogo | Fornece categoria e modelos: P4/P5, RN-010. |
| Persistência de pedidos | Recupera e confirma dados, desfaz falhas e registra erros sem dados pessoais: P3/P8, RN-012/024, RNF-006/007. |
| `alt`, `break`, `loop` | Decisões, encerramentos e análise dos candidatos: fluxos e RN-010. |

As camadas são decisões de projeto para executar esses comportamentos.

[Especificação completa do UC-017](https://github.com/Matheus-lc/vazaapp-aps/blob/main/README.md#uc-017)
