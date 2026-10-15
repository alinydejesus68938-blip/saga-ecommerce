# Diagrama de Sequência — Falha e Compensação da Saga

## Objetivo

Demonstrar como o sistema trata a recusa do pagamento depois da reserva do estoque, executando transações compensatórias para manter a consistência dos dados.

## Fluxo de falha e compensação

```mermaid id="w2k9xp"
sequenceDiagram
    actor Cliente
    participant O as Order Service
    participant S as Saga Orchestrator
    participant I as Inventory Service
    participant P as Payment Service
    participant G as Gateway Financeiro

    Cliente->>O: Solicita compra
    O->>O: Cria pedido PENDENTE
    O-->>S: Inicia Saga

    S->>I: Reserva estoque
    I-->>S: Reserva confirmada

    S->>P: Processa pagamento
    P->>G: Solicita autorização
    G-->>P: Pagamento recusado
    P-->>S: Falha no pagamento

    S->>O: Solicita cancelamento do pedido
    O->>O: Atualiza status para CANCELADO
    O-->>S: Cancelamento confirmado

    S->>I: Solicita liberação do estoque
    I->>I: Executa compensação da reserva
    I-->>S: Estoque liberado

    S->>S: Registra Saga como COMPENSADA
    S-->>Cliente: Compra não concluída
```

## Estratégia de compensação

- **Cancel Order:** o pedido é marcado como cancelado.
- **Release Stock:** a reserva de estoque é desfeita, disponibilizando novamente os produtos.
- **Registro da Saga:** o orquestrador persiste o resultado da compensação e permite acompanhar a operação.

## Tratamento de falhas

As compensações devem ser idempotentes e possuir retentativas controladas. Se uma compensação falhar temporariamente, o sistema mantém a Saga em estado de compensação pendente até conseguir concluir a operação ou encaminhá-la para análise operacional.
