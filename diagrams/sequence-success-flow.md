# Diagrama de Sequência — Compra com Sucesso

## Objetivo

Representar a sequência de comunicação entre os microsserviços quando uma compra é concluída com sucesso.

## Fluxo de sucesso

```mermaid
sequenceDiagram
    actor Cliente
    participant O as Order Service
    participant S as Saga Orchestrator
    participant I as Inventory Service
    participant P as Payment Service
    participant G as Gateway Financeiro

    Cliente->>O: Solicita compra
    O->>O: Registra pedido PENDENTE
    O-->>S: Inicia Saga com ID único

    S->>I: Solicita reserva de estoque
    I->>I: Verifica disponibilidade e reserva
    I-->>S: Estoque reservado

    S->>P: Solicita processamento do pagamento
    P->>G: Autoriza cobrança
    G-->>P: Pagamento aprovado
    P-->>S: Pagamento confirmado

    S->>O: Confirma pedido
    O->>O: Atualiza status para CONFIRMADO
    O-->>Cliente: Compra concluída

    S->>S: Registra Saga como CONCLUÍDA
```

## Resultado esperado

O estoque fica reservado, o pagamento é aprovado e o pedido é confirmado. Cada etapa deve ser idempotente, evitando efeitos duplicados em caso de reenvio de mensagens.
