# Arquitetura de Microsserviços — Padrão Saga

## 1. Contexto do projeto

Este projeto apresenta uma proposta de arquitetura distribuída para um sistema de comércio eletrônico que está migrando de um monólito para microsserviços independentes.

A compra envolve três serviços, cada um com seu próprio banco de dados:

- **Order Service:** cria e gerencia os pedidos.
- **Inventory Service:** verifica e reserva produtos em estoque.
- **Payment Service:** processa pagamentos por meio de gateways externos.

Como não existe uma única transação ACID abrangendo todos os serviços, será utilizado o padrão Saga para coordenar operações locais e realizar compensações em caso de falha.

## 2. Estratégia escolhida: Saga por orquestração

A solução utiliza um orquestrador central responsável por controlar o estado da Saga e coordenar os serviços participantes.

Essa abordagem foi escolhida porque facilita a visualização do fluxo de compra, centraliza o tratamento de falhas e torna explícita a sequência de compensações.

## 3. Fluxo de sucesso

1. O cliente solicita uma compra.
2. O Order Service registra o pedido como `PENDING`.
3. O orquestrador solicita a reserva de estoque.
4. O Inventory Service reserva os produtos e confirma a operação.
5. O orquestrador solicita o processamento do pagamento.
6. O Payment Service aprova o pagamento.
7. O Order Service atualiza o pedido para `APPROVED`.

## 4. Fluxo de falha e compensação

Se o pagamento for recusado após a reserva do estoque:

1. O Payment Service informa a recusa.
2. O orquestrador inicia as compensações.
3. O Inventory Service libera a reserva de estoque.
4. O Order Service atualiza o pedido para `CANCELLED`.
5. A Saga registra o resultado final e disponibiliza o motivo da falha.

As compensações são novas operações de negócio; elas não apagam retroativamente as transações já realizadas.

## 5. Resiliência e consistência

A arquitetura prevê:

- Consumidores idempotentes para evitar efeitos duplicados.
- Identificadores únicos de Saga e de mensagem.
- Retentativas controladas para falhas transitórias.
- Timeouts e tratamento de resultados incertos.
- Dead-letter queue para mensagens que excedam as tentativas permitidas.
- Registro persistente do estado da Saga.
- Padrão Transactional Outbox para publicar eventos com confiabilidade.

## 6. Estrutura dos entregáveis

- `diagrams/saga-architecture-overview.png`: imagem da arquitetura geral da Saga.
- `diagrams/sequence-success-flow.png`: imagem da sequência de sucesso.
- `diagrams/sequence-compensation-flow.png`: imagem da sequência de falha e compensação.

## 7. Objetivo
Demonstrar como coordenar transações distribuídas em microsserviços, mantendo consistência eventual e tratando falhas por meio de transações compensatórias.

## Documentação

- [Decisões arquiteturais do projeto Saga](docs/decisoes-arquiteturais.md)


### Diagramas da arquitetura

- [Visão geral da arquitetura Saga](diagrams/saga-architecture-overview.md)
- [Fluxo de sucesso da compra](diagrams/sequence-success-flow.md)
- [Fluxo de falha e compensação](diagrams/sequence-compensation-flow.md)
- [Fluxo completo da transação Saga](diagrams/fluxo-saga.md)


