# Tarefas Automatizadas com Amazon S3 e AWS Lambda

> Material de apoio para consultas rápidas sobre automações utilizando Amazon S3, AWS Lambda, DynamoDB e LocalStack. O foco é compreender como esses serviços trabalham em conjunto na construção de aplicações serverless orientadas a eventos.

---

# O que é Computação Serverless?

Serverless é um modelo onde o desenvolvedor se preocupa apenas com a lógica da aplicação.

A AWS é responsável por:

- Provisionamento de servidores
- Escalabilidade
- Alta disponibilidade
- Atualizações da infraestrutura

O desenvolvedor apenas envia o código.

Exemplo:

```
Upload Arquivo

↓

S3

↓

Lambda

↓

Processamento

↓

DynamoDB
```

---

# Tarefas Automatizadas com Amazon S3 e Lambda

Uma das integrações mais utilizadas na AWS é entre o **Amazon S3** e o **AWS Lambda**.

Sempre que um evento ocorre no S3, ele pode disparar automaticamente uma função Lambda.

Esses eventos incluem:

- Upload de arquivos
- Exclusão de arquivos
- Alteração de objetos
- Cópia de arquivos

Fluxo:

```
Usuário envia arquivo

↓

Amazon S3

↓

Evento

↓

AWS Lambda

↓

Processamento
```

Não é necessário criar servidores monitorando o Bucket.

---

# Casos de Uso

Essa arquitetura é muito utilizada para:

- Processamento de imagens
- Conversão de arquivos
- ETL
- Validação de documentos
- Processamento de planilhas
- Geração de PDFs
- Extração de metadados
- Registro em bancos de dados
- Envio de notificações

---

# Entendendo o Amazon S3

O **Amazon Simple Storage Service (S3)** é o serviço de armazenamento de objetos da AWS.

Ele armazena qualquer tipo de arquivo.

Exemplos:

- imagens
- vídeos
- documentos
- backups
- arquivos CSV
- arquivos JSON
- logs

---

# Conceitos importantes

## Bucket

É o "container" onde os arquivos ficam armazenados.

```
Bucket

├── imagem.jpg

├── clientes.csv

├── backup.zip

└── dados.json
```

Cada Bucket possui um nome globalmente único.

---

## Object

Cada arquivo armazenado no S3 é chamado de **Object**.

Todo objeto possui:

- conteúdo
- nome (Key)
- metadados

---

## Key

É o caminho completo do arquivo dentro do Bucket.

Exemplo:

```
clientes/2026/junho/clientes.csv
```

A Key identifica unicamente um objeto dentro do Bucket.

---

# Eventos do S3

O S3 pode gerar eventos automaticamente.

Exemplos:

```
ObjectCreated

ObjectRemoved

ObjectRestore

ObjectTagging
```

Esses eventos podem acionar:

- Lambda
- SNS
- SQS
- EventBridge

---

# Vantagens do S3

- Alta durabilidade dos dados.
- Escalabilidade automática.
- Armazenamento praticamente ilimitado.
- Integração com diversos serviços AWS.
- Versionamento opcional.
- Criptografia de dados.

---

# Entendendo o AWS Lambda

O **AWS Lambda** é o serviço de computação serverless da AWS.

Sua função é executar código sob demanda.

Não é necessário:

- criar servidores
- configurar sistema operacional
- instalar software
- administrar infraestrutura

---

# Como funciona?

Fluxo:

```
Evento

↓

Lambda

↓

Executa código

↓

Resposta
```

A execução ocorre apenas quando há um evento.

---

# Eventos que podem acionar uma Lambda

- Upload no S3
- Mensagens do SQS
- Requisições da API Gateway
- Eventos do EventBridge
- Alterações no DynamoDB
- Execução pelo Step Functions

---

# Estrutura de uma Lambda

Uma função Lambda normalmente possui:

- Código
- Handler
- Runtime
- Role IAM
- Variáveis de Ambiente

---

# Runtime

A Lambda suporta diversas linguagens.

Exemplos:

- Python
- Node.js
- Java
- .NET
- Go
- Ruby

---

# Handler

É o ponto de entrada da função.

Quando a Lambda é executada, o Handler é chamado automaticamente.

Exemplo em Python:

```python
def lambda_handler(event, context):
    return "Olá AWS!"
```

---

# Event

O parâmetro `event` contém os dados enviados pelo serviço que disparou a Lambda.

Quando o gatilho é um Bucket S3:

```json
{
  "Records": [
    {
      "s3": {
        "bucket": {
          "name": "meu-bucket"
        },
        "object": {
          "key": "clientes.csv"
        }
      }
    }
  ]
}
```

Essas informações permitem que a função saiba qual arquivo foi enviado.

---

# Context

O parâmetro `context` contém informações da execução.

Exemplos:

- Nome da função
- Tempo restante
- Request ID
- Limite de memória

---

# Upload de Arquivos com Processamento e Registro no DynamoDB

Um fluxo bastante comum é:

```
Upload CSV

↓

Amazon S3

↓

Evento

↓

Lambda

↓

Leitura do arquivo

↓

Validação

↓

Processamento

↓

DynamoDB
```

---

# Exemplo de processamento

Imagine um arquivo:

```
clientes.csv
```

Conteúdo:

```
Nome,Idade

Ana,25

Carlos,31

Maria,42
```

Fluxo:

```
Upload

↓

S3

↓

Lambda

↓

Ler CSV

↓

Validar dados

↓

Salvar registros

↓

DynamoDB
```

---

# Por que utilizar DynamoDB?

O DynamoDB é um banco NoSQL totalmente gerenciado.

Pode ser utilizado para:

- armazenar metadados
- registrar uploads
- controlar processamento
- armazenar resultados
- registrar auditoria

Exemplo:

| Arquivo | Status |
|----------|---------|
| clientes.csv | Processado |
| vendas.csv | Em andamento |

---

# Permissões IAM

Para que a Lambda funcione corretamente, ela precisa de permissões.

Exemplo:

```
Lambda

↓

Role IAM

↓

Permissão para:

- Ler Bucket S3
- Escrever DynamoDB
- Criar Logs
```

Sem essas permissões a execução falhará.

---

# CloudWatch Logs

Toda execução da Lambda pode gerar logs.

Esses logs ficam disponíveis no CloudWatch.

É possível visualizar:

- Erros
- Tempo de execução
- Informações de debug
- Prints do código

---

# Configurando AWS Localmente com LocalStack

O **LocalStack** é uma ferramenta que simula diversos serviços da AWS em um ambiente local.

Ele permite desenvolver e testar aplicações sem utilizar recursos reais da AWS.

---

# Benefícios

- Desenvolvimento offline
- Testes rápidos
- Menor custo
- Ambiente isolado
- Facilidade para aprendizado

---

# Serviços suportados

Entre os principais:

- S3
- Lambda
- DynamoDB
- SQS
- SNS
- IAM (parcial)
- CloudFormation
- API Gateway

---

# Fluxo com LocalStack

```
Aplicação

↓

LocalStack

↓

S3

↓

Lambda

↓

DynamoDB
```

Todo o fluxo acontece localmente.

---

# Criando os Recursos

Um ambiente típico possui:

```
Bucket S3

↓

Lambda

↓

Tabela DynamoDB
```

Esses recursos podem ser criados por:

- AWS CLI
- CloudFormation
- Terraform
- Scripts automatizados

---

# Trabalhando com Arquivos Localmente

Durante o desenvolvimento é comum realizar testes sem utilizar arquivos reais da produção.

Fluxo:

```
Arquivo Local

↓

Upload para Bucket

↓

Evento

↓

Lambda

↓

Processamento

↓

Resultado
```

Esse processo pode ser repetido diversas vezes durante os testes.

---

# Fluxo Completo

```
Usuário

↓

Upload

↓

Amazon S3

↓

Evento ObjectCreated

↓

AWS Lambda

↓

Leitura do Arquivo

↓

Validação

↓

Processamento

↓

DynamoDB

↓

CloudWatch Logs
```

---

# Boas Práticas

- Criar Lambdas pequenas e especializadas.
- Utilizar um Bucket apenas para arquivos de entrada quando possível.
- Registrar logs suficientes no CloudWatch.
- Validar o tipo do arquivo antes do processamento.
- Evitar armazenar arquivos temporários permanentemente.
- Utilizar variáveis de ambiente para configurações.
- Aplicar o princípio do menor privilégio nas permissões IAM.
- Processar arquivos grandes em partes (quando necessário), evitando exceder o tempo máximo de execução da Lambda.
- Utilizar Dead Letter Queues (DLQ) ou destinos de falha para tratar erros persistentes em processamentos assíncronos.

---

# Insights

## Arquitetura Orientada a Eventos

O S3 não precisa "chamar" diretamente a Lambda.

Na realidade, um evento é emitido automaticamente quando ocorre uma ação no Bucket.

```
Evento

↓

Lambda

↓

Processamento
```

Essa arquitetura desacopla os serviços.

---

## Excelente para ETL

Uma arquitetura muito utilizada é:

```
Upload CSV

↓

S3

↓

Lambda

↓

Transformação

↓

DynamoDB

↓

Athena

↓

QuickSight
```

---

## Escalabilidade Automática

Se vários arquivos forem enviados ao Bucket, o Lambda poderá executar múltiplas instâncias da função em paralelo (respeitando os limites de concorrência configurados para a conta ou função).

---

## Desenvolvimento Local

Utilizar LocalStack permite desenvolver praticamente toda a arquitetura antes do deploy na AWS.

Isso reduz custos e acelera o ciclo de desenvolvimento.

---

# Serviços Integrados

| Serviço | Função |
|----------|--------|
| Amazon S3 | Armazenamento de arquivos |
| AWS Lambda | Processamento serverless |
| DynamoDB | Armazenamento de dados processados |
| IAM | Controle de permissões |
| CloudWatch | Logs e monitoramento |
| EventBridge | Roteamento de eventos |
| SQS | Filas para processamento assíncrono |
| SNS | Notificações |
| LocalStack | Simulação local dos serviços AWS |

---

# Resumo

- O Amazon S3 é o serviço de armazenamento de objetos da AWS.
- O AWS Lambda executa código automaticamente em resposta a eventos.
- Uploads no S3 podem disparar funções Lambda sem necessidade de servidores.
- O DynamoDB pode armazenar os resultados ou metadados do processamento.
- O CloudWatch registra logs e métricas das execuções.
- O LocalStack permite desenvolver e testar arquiteturas AWS localmente.
- A combinação S3 + Lambda é uma das arquiteturas serverless mais utilizadas para automação de tarefas, processamento de arquivos e pipelines de dados.
