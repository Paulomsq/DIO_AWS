# AWS Step Functions - Anotações de Aula

> Material de apoio para consultas rápidas sobre AWS Step Functions, integração com Lambda e S3, e conceitos relacionados à orquestração de fluxos de trabalho.

---

# O que é o AWS Step Functions?

O **AWS Step Functions** é um serviço de **orquestração de workflows** da AWS.

Seu objetivo é controlar a execução de diversas tarefas (Steps), permitindo que serviços diferentes trabalhem juntos de maneira organizada, resiliente e visual.

Em vez de escrever toda a lógica de fluxo dentro do código de uma aplicação, o fluxo passa a ser definido em um **State Machine (Máquina de Estados)**.

---

# Quando utilizar?

O Step Functions é recomendado quando existe um processo composto por várias etapas, como:

- Execução de múltiplas funções Lambda
- Processos ETL
- Pipelines de dados
- Aprovação de processos
- Automações
- Processamento de arquivos
- Integração entre diversos serviços AWS

Exemplo:

```
Upload de arquivo
        ↓
Validação
        ↓
Transformação
        ↓
Salvar no S3
        ↓
Enviar notificação
```

Cada etapa pode ser executada por um serviço diferente.

---

# Benefícios

## Separação da lógica

A lógica do fluxo fica fora do código da aplicação.

Ao invés de:

```
Lambda
 ├─ chama outra Lambda
 ├─ faz validação
 ├─ tenta novamente
 ├─ envia e-mail
 └─ trata erros
```

Temos:

```
Step Functions

Estado 1
      ↓
Estado 2
      ↓
Estado 3
      ↓
Estado 4
```

Cada Lambda fica responsável apenas por sua tarefa.

---

## Visualização

O console mostra todo o fluxo graficamente.

É possível visualizar:

- qual etapa está executando
- qual falhou
- tempo gasto
- entrada e saída dos estados

Isso facilita bastante o debugging.

---

## Retry automático

É possível configurar tentativas automáticas sem alterar o código.

Exemplo:

```
Lambda falhou

↓

Retry 1

↓

Retry 2

↓

Retry 3

↓

Falha definitiva
```

Isso evita implementar retries manualmente.

---

## Tratamento de erros

Também é possível definir caminhos diferentes dependendo do erro.

Exemplo:

```
Processar pagamento

        ↓

Sucesso → Continua

Erro → Enviar alerta
```

---

## Escalabilidade

O serviço é totalmente gerenciado.

Não há necessidade de:

- criar servidores
- configurar disponibilidade
- balanceamento
- infraestrutura

---

# Conceito de State Machine

Toda execução acontece dentro de uma **State Machine**.

Ela descreve:

- estados
- ordem de execução
- decisões
- paralelismo
- tratamento de erro
- fim da execução

O Step Functions utiliza a linguagem **Amazon States Language (ASL)** em formato JSON.

---

# Principais tipos de Estados

## Task

Executa uma ação.

Exemplo:

- Lambda
- ECS
- Glue
- Batch
- SageMaker

---

## Pass

Não executa processamento.

Serve para:

- organizar fluxo
- inserir valores
- testes

---

## Choice

Funciona como um `if/else`.

Exemplo:

```
Valor > 100?

Sim → Aprovação

Não → Finalizar
```

---

## Wait

Espera um período antes de continuar.

Pode esperar:

- segundos
- timestamp específico

Muito utilizado em integrações.

---

## Parallel

Executa vários fluxos ao mesmo tempo.

```
           Processo

         /          \

Validação      Geração PDF

         \          /

          Continua
```

---

## Map

Executa a mesma operação para vários itens.

Exemplo:

Lista de arquivos:

```
arquivo1.csv

arquivo2.csv

arquivo3.csv
```

Cada arquivo pode ser processado independentemente.

Muito utilizado em ETL.

---

## Succeed

Indica sucesso.

---

## Fail

Finaliza informando erro.

---

# Standard x Express Workflows

## Standard

Características:

- duração de até 1 ano
- histórico completo
- ideal para processos críticos
- maior rastreabilidade

Exemplos:

- ETL
- processos financeiros
- workflows corporativos

---

## Express

Características:

- alta taxa de execução
- curta duração
- menor custo para grandes volumes
- histórico reduzido

Exemplos:

- APIs
- eventos
- processamento em massa

---

# Projeto Modelo

Um fluxo bastante comum:

```
Usuário envia arquivo

↓

S3

↓

Step Functions

↓

Lambda Validação

↓

Lambda Transformação

↓

Salvar resultado no S3

↓

Notificação SNS
```

---

# Integração com Lambda

É uma das integrações mais utilizadas.

Cada Lambda executa uma responsabilidade específica.

Exemplo:

```
Lambda 1

↓

Validar dados

↓

Lambda 2

↓

Transformar

↓

Lambda 3

↓

Salvar
```

Vantagens:

- funções menores
- código desacoplado
- manutenção facilitada
- reutilização

---

# Integração com S3

O S3 normalmente é utilizado para armazenar:

- arquivos de entrada
- arquivos processados
- relatórios
- logs
- backups

Fluxo comum:

```
Upload

↓

S3

↓

Step Functions

↓

Lambda lê arquivo

↓

Processamento

↓

Novo arquivo

↓

S3
```

---

# Validações utilizando Step Functions

Uma boa prática é dividir validações.

Exemplo:

```
Receber arquivo

↓

Arquivo existe?

↓

Formato válido?

↓

Conteúdo válido?

↓

Salvar
```

Cada validação pode ser uma Lambda diferente.

Isso facilita:

- manutenção
- testes
- reutilização

---

# Entrada e Saída dos Estados

Cada estado recebe um JSON.

Exemplo:

Entrada

```json
{
  "arquivo": "clientes.csv"
}
```

Saída

```json
{
  "arquivo": "clientes.csv",
  "linhas": 500
}
```

Esse JSON é passado para o próximo estado.

---

# JSONPath

O Step Functions utiliza JSONPath para selecionar dados.

Exemplo:

```
$.cliente.nome

$.pedido.id

$.arquivo
```

Assim evita enviar dados desnecessários entre estados.

---

# Logs

É possível integrar com:

- CloudWatch Logs
- CloudWatch Metrics
- CloudWatch Alarms

Facilitando:

- monitoramento
- auditoria
- troubleshooting

---

# IAM

O Step Functions executa utilizando uma Role IAM.

Essa Role precisa de permissões para acessar serviços como:

- Lambda
- S3
- SNS
- DynamoDB
- Glue
- ECS

Princípio importante:

> Sempre conceder apenas as permissões necessárias (Least Privilege).

---

# Custos

O Step Functions possui cobrança baseada em execução.

De forma simplificada:

**Standard**

- cobrança por transição entre estados.

**Express**

- cobrança por quantidade de execuções, duração e memória utilizada.

Sempre consulte a calculadora oficial da AWS para estimativas atualizadas.

---

# Boas práticas

- Criar Lambdas pequenas e especializadas.
- Não concentrar toda a lógica em uma única função.
- Aproveitar Retry e Catch ao invés de implementar tentativas manualmente.
- Utilizar Choice para decisões ao invés de vários ifs dentro da Lambda.
- Registrar logs suficientes para facilitar investigações.
- Utilizar IAM com menor privilégio possível.
- Evitar enviar grandes volumes de dados entre estados; prefira armazenar objetos grandes no S3 e compartilhar apenas a chave (key) do objeto.

---

# Insights

## Step Functions não substitui Lambda

Lambda executa código.

Step Functions coordena a execução.

Os dois serviços normalmente trabalham juntos.

---

## Excelente para ETL

Fluxo típico:

```
S3

↓

Extrair

↓

Validar

↓

Transformar

↓

Carregar

↓

S3

↓

Athena

↓

QuickSight
```

---

## Facilita manutenção

Adicionar uma nova etapa normalmente significa apenas inserir um novo estado na máquina de estados, sem alterar significativamente as demais etapas.

---

## Redução de complexidade

Em vez de escrever código responsável por:

- retry
- timeout
- if/else
- paralelismo
- tratamento de erro

essas responsabilidades podem ser declaradas diretamente no workflow.

---

# Serviços AWS frequentemente integrados

| Serviço | Utilização |
|----------|------------|
| Lambda | Processamento de código |
| S3 | Armazenamento de arquivos |
| DynamoDB | Persistência de dados |
| SNS | Notificações |
| SQS | Filas |
| EventBridge | Disparo de eventos |
| Glue | ETL |
| ECS/Fargate | Containers |
| Batch | Processamentos em lote |
| Athena | Consultas em arquivos no S3 |
| CloudWatch | Logs e monitoramento |

---

# Resumo

- Step Functions é um serviço de orquestração de workflows.
- O fluxo é definido por uma máquina de estados (State Machine).
- Cada etapa é um State.
- Integra-se nativamente com diversos serviços AWS.
- Lambda executa o processamento.
- S3 normalmente armazena entradas e saídas do fluxo.
- Retry, tratamento de erros, paralelismo e decisões fazem parte do serviço.
- É amplamente utilizado em pipelines de dados, ETLs, automações e integrações entre serviços.
