
# Decisões Arquiteturais — Projeto Saga E-commerce

## 1. Contexto do projeto

O projeto propõe uma arquitetura de microserviços para um sistema de comércio eletrônico, permitindo que cada serviço execute suas responsabilidades de forma independente.

A compra envolve três serviços principais:

- **Order Service:** cria e gerencia pedidos.
- **Inventory Service:** verifica e reserva produtos em estoque.
- **Payment Service:** processa pagamentos.

## 2. Estratégia escolhida: Saga por orquestração

Foi escolhido o padrão Saga por orquestração, no qual um orquestrador central coordena as etapas da transação distribuída.

Essa abordagem facilita o acompanhamento do processo de compra e permite executar ações compensatórias quando ocorre uma falha.

## 3. Fluxo da transação

1. O cliente realiza um pedido.
2. O Order Service registra o pedido.
3. O orquestrador solicita a reserva do estoque.
4. O Inventory Service confirma ou rejeita a reserva.
5. O Payment Service tenta processar o pagamento.
6. Se todas as etapas forem concluídas, o pedido é confirmado.
7. Se ocorrer uma falha, o sistema executa as ações compensatórias necessárias.

## 4. Tratamento de falhas

- Falha no estoque: o pedido não prossegue para o pagamento.
- Falha no pagamento: o estoque reservado é liberado e o pedido é cancelado.
- Falha de comunicação: podem ser utilizadas tentativas controladas, registro de eventos e monitoramento.

As compensações não representam um rollback global de banco de dados. Elas são operações específicas executadas pelos serviços participantes.

## 5. Justificativa da arquitetura

A estratégia escolhida favorece a organização do fluxo de compra, a rastreabilidade das etapas e a recuperação diante de falhas.

O orquestrador centraliza a coordenação, enquanto os microserviços mantêm suas responsabilidades e seus próprios dados.

## 6. Tecnologias e ferramentas

As tecnologias serão definidas conforme a implementação do projeto, considerando linguagens de programação, APIs, persistência de dados, comunicação entre serviços, testes e controle de versão com Git e GitHub.

## 7. Conclusão

O padrão Saga por orquestração oferece uma estratégia para coordenar transações distribuídas no comércio eletrônico, mantendo a consistência do processo por meio de confirmações, compensações e tratamento de falhas.
