# Desafio 04 - Orquestração de Workflows com AWS Step Functions
Repositório do quarto desafio do curso Cloud com AWS da [DIO](https://www.dio.me/). O objetivo foi explorar o AWS Step Functions, serviço de orquestração de fluxos de trabalho que permite integrar e automatizar serviços da AWS (como Lambda, S3, SNS, SQS e DynamoDB) de forma visual e com pouco código, utilizando o recurso de Mapa Distribuído (Distributed Map) para processar arquivos em um bucket S3.
## O que eu aprendi

* Step Functions: serviço de orquestração que coordena a execução de múltiplos serviços da AWS através de workflows visuais (máquinas de estado), sem precisar escrever lógica de coordenação manualmente.
* State Machine: é a definição do workflow em si, composta por estados (tarefas, escolhas, paralelismos etc.) que determinam o fluxo de execução.
* Distributed Map: recurso do Step Functions que permite processar em larga escala um grande volume de itens (ex: milhares de arquivos em um bucket S3) em paralelo, sem precisar orquestrar isso manualmente.
* Integração com S3: o Distributed Map pode listar os objetos de um bucket diretamente como fonte de entrada do workflow, aplicando uma mesma lógica de processamento a cada arquivo.

## Arquitetura
Diagrama representando o workflow criado no Step Functions, com o estado de Mapa Distribuído processando os arquivos do bucket S3:

![Diagrama do workflow](imagens/diagrama-workflow.png)

## O que eu fiz na prática

1. Criei um bucket S3 (`desafio-step-functions-arquivos`) e subi 3 arquivos de teste para servirem de entrada para o workflow

2. Criei a State Machine no Step Functions em modo **Standard**, usando o editor visual (Workflow Studio)

3. Adicionei o estado **Map** e configurei o **Processing mode** como **Distributed**, com **Item source** apontando para o bucket S3 criado

4. Adicionei um estado **Pass** dentro do Map (ItemProcessor) para representar o processamento de cada item

5. Corrigi um erro de validação (ItemProcessor vazio) ao garantir que o estado Pass fosse solto corretamente dentro do bloco Map

6. Executei o workflow (**Start execution**) e acompanhei a execução na Graph view

![Execução do workflow](imagens/execucao-workflow.png)

7. Confirmei que o Map processou os 3 arquivos do bucket com sucesso (bloco todo verde, `Item source: desafio-step-functions-arquivos`)

## Minhas impressões
A configuração do Mapa Distribuído em si foi tranquila pelo editor visual, mas esbarrei em um erro de validação porque o estado "Pass" não tinha sido solto exatamente dentro da área do Map (ItemProcessor) — o erro `ItemProcessor/StartAt: Value cannot be an empty string` deixou isso claro. Depois de corrigir, o workflow rodou sem problemas, processando os 3 arquivos do bucket S3 automaticamente. Ficou evidente como o Step Functions abstrai a orquestração: não precisei escrever nenhuma lógica de loop ou controle de concorrência para processar múltiplos arquivos, bastou apontar o Map para o bucket. Pretendo usar esse conhecimento para automatizar pipelines de processamento de arquivos em lote (ex: validação de notas fiscais, transformação de dados) sem precisar gerenciar a orquestração manualmente.

## Referências

* [AWS Step Functions - Documentação AWS](https://aws.amazon.com/pt/step-functions/)

Feito durante o curso Cloud com AWS da [DIO](https://www.dio.me/).