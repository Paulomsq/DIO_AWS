# AWS CloudFormation - Anotações de Aula

> Material de apoio para consultas rápidas sobre AWS CloudFormation, criação de Stacks e utilização do serviço para provisionamento de infraestrutura, incluindo exemplos voltados para Firewall, Lambda e S3.

---

# O que é o AWS CloudFormation?

O **AWS CloudFormation** é o serviço de **Infraestrutura como Código (Infrastructure as Code - IaC)** da AWS.

Seu objetivo é permitir que toda a infraestrutura da AWS seja criada, atualizada e removida através de arquivos chamados **Templates**, eliminando a necessidade de criar recursos manualmente pelo Console.

Em outras palavras:

> O CloudFormation transforma arquivos de configuração em infraestrutura real na AWS.

---

# Por que utilizar Infraestrutura como Código (IaC)?

Antes do CloudFormation era comum criar recursos manualmente:

- Criar um Bucket S3
- Criar uma Lambda
- Configurar permissões IAM
- Criar uma VPC
- Configurar Security Groups

Esse processo apresenta alguns problemas:

- Erros humanos
- Falta de padronização
- Dificuldade para recriar ambientes
- Configurações diferentes entre Desenvolvimento e Produção

Com o CloudFormation:

```
Template

↓

CloudFormation

↓

Infraestrutura pronta
```

Toda a infraestrutura pode ser recriada quantas vezes forem necessárias.

---

# Principais Benefícios

## Automação

Cria dezenas ou centenas de recursos automaticamente.

Exemplo:

```
Template

↓

VPC

↓

Subnets

↓

IAM

↓

Lambda

↓

S3

↓

CloudWatch
```

---

## Padronização

Todos os ambientes possuem exatamente a mesma configuração.

Exemplo:

```
Desenvolvimento

↓

Homologação

↓

Produção

↓

Mesmo Template
```

---

## Versionamento

Os Templates ficam armazenados em repositórios Git.

Assim é possível:

- acompanhar alterações
- revisar mudanças
- recuperar versões anteriores

---

## Reprodutibilidade

Caso um ambiente precise ser recriado, basta executar novamente o Template.

Não é necessário repetir configurações manualmente.

---

## Gerenciamento Centralizado

O CloudFormation controla todos os recursos criados por uma Stack.

Caso seja necessário remover a infraestrutura:

```
Excluir Stack

↓

Todos os recursos são removidos
```

*(Exceto quando configurados para serem preservados.)*

---

# Templates

Os Templates descrevem toda a infraestrutura.

São escritos em:

- YAML (mais utilizado)
- JSON

Exemplo:

```yaml
Resources:
  MeuBucket:
    Type: AWS::S3::Bucket
```

---

# Estrutura de um Template

Os principais blocos são:

```text
AWSTemplateFormatVersion

Description

Parameters

Mappings

Conditions

Resources

Outputs
```

Nem todos são obrigatórios.

---

# Resources

É a parte mais importante do Template.

Tudo que será criado fica dentro dela.

Exemplo:

```yaml
Resources:
```

Dentro desse bloco podem existir:

- Buckets S3
- Lambdas
- IAM Roles
- VPC
- EC2
- DynamoDB
- CloudWatch
- Security Groups

---

# Parameters

Permitem que o Template receba informações durante a criação.

Exemplo:

```
Nome do Bucket

↓

Template

↓

Bucket criado com esse nome
```

Isso evita criar diversos Templates diferentes.

---

# Outputs

Permitem retornar informações após a criação da Stack.

Exemplo:

- Nome do Bucket
- ARN da Lambda
- Endpoint de um Load Balancer
- URL de uma API

Essas informações podem ser utilizadas por outros Templates ou aplicações.

---

# O que é uma Stack?

Uma **Stack** é o conjunto de recursos criados por um Template.

Exemplo:

```
Template

↓

CloudFormation

↓

Stack

↓

Bucket S3

Lambda

IAM

CloudWatch
```

A Stack representa toda a infraestrutura daquele projeto.

---

# Ciclo de Vida de uma Stack

Uma Stack pode ser:

- Criada
- Atualizada
- Excluída

Fluxo:

```
Template

↓

Create Stack

↓

Infraestrutura criada

↓

Update Stack

↓

Infraestrutura atualizada

↓

Delete Stack

↓

Infraestrutura removida
```

---

# Criando Stacks no AWS CloudFormation

## Passo 1

Criar o Template.

Exemplo:

```
infraestrutura.yaml
```

---

## Passo 2

Enviar o Template para o CloudFormation.

Pode ser feito:

- Console AWS
- AWS CLI
- SDK
- Pipeline CI/CD

---

## Passo 3

Criar a Stack.

Durante esse processo podem ser informados:

- Nome da Stack
- Parâmetros
- Tags
- Permissões IAM

---

## Passo 4

O CloudFormation cria automaticamente todos os recursos.

Exemplo:

```
Stack

↓

Bucket

↓

Lambda

↓

IAM

↓

CloudWatch
```

---

# Atualizando uma Stack

Quando o Template é alterado:

```
Novo Template

↓

Update Stack

↓

CloudFormation compara

↓

Aplica somente as mudanças necessárias
```

Essa comparação reduz alterações desnecessárias.

---

# Change Sets

Antes de atualizar uma Stack é possível visualizar:

- recursos que serão criados
- recursos alterados
- recursos removidos

Isso reduz riscos em ambientes produtivos.

---

# Rollback Automático

Se ocorrer algum erro durante a criação:

```
Criando recursos

↓

Erro

↓

Rollback

↓

Infraestrutura retorna ao estado anterior
```

Essa funcionalidade evita ambientes parcialmente configurados.

---

# Dependência entre Recursos

O CloudFormation identifica automaticamente dependências.

Exemplo:

Uma Lambda precisa de uma IAM Role.

O CloudFormation cria:

```
IAM Role

↓

Lambda
```

Mesmo que ambas estejam no mesmo Template.

---

# Integração com Lambda

É possível criar funções Lambda diretamente pelo CloudFormation.

Além da função, também podem ser criados:

- IAM Role
- Permissões
- Variáveis de ambiente
- Triggers
- Logs

Tudo em um único Template.

---

# Integração com S3

Um Template pode criar Buckets S3 automaticamente.

Também é possível configurar:

- Versionamento
- Criptografia
- Bloqueio de acesso público
- Políticas de acesso
- Lifecycle Rules

---

# Criando Stacks de Firewall no CloudFormation

Um dos usos mais comuns do CloudFormation é automatizar a criação de regras de segurança.

Na AWS, o conceito de "firewall" pode envolver diferentes serviços, como:

- **Security Groups** (controle de tráfego de instâncias EC2, RDS, ECS etc.)
- **Network ACLs (NACLs)** (controle de tráfego em nível de sub-rede)
- **AWS Network Firewall** (firewall gerenciado para redes VPC)
- **AWS WAF** (proteção de aplicações web contra ataques)

Todos esses recursos podem ser definidos em Templates do CloudFormation.

---

# Exemplo: Security Group

Fluxo:

```
Template

↓

Security Group

↓

Porta 443 liberada

↓

Porta 22 restrita

↓

Aplicação protegida
```

Um Security Group define quais portas e protocolos podem acessar um recurso.

Exemplo de regras:

- HTTPS (443)
- HTTP (80)
- SSH (22) apenas para IPs autorizados

---

# Exemplo: AWS WAF

Fluxo:

```
Internet

↓

AWS WAF

↓

Application Load Balancer

↓

Aplicação
```

Com CloudFormation é possível criar regras como:

- Bloqueio de SQL Injection
- Bloqueio de Cross-Site Scripting (XSS)
- Rate Limiting
- Bloqueio por IP
- Listas de IP permitidos ou negados

---

# Exemplo: AWS Network Firewall

Fluxo:

```
Internet

↓

Network Firewall

↓

VPC

↓

Servidores
```

Pode ser utilizado para controlar o tráfego entre redes, inspecionar pacotes e aplicar políticas centralizadas.

---

# Exemplo de Arquitetura Completa

```
CloudFormation

↓

VPC

↓

Subnets

↓

Security Group

↓

Lambda

↓

Bucket S3

↓

CloudWatch

↓

Aplicação pronta
```

Toda essa infraestrutura pode ser criada com uma única Stack.

---

# Boas Práticas

- Utilizar YAML por ser mais legível.
- Separar Templates muito grandes em módulos menores.
- Utilizar Parameters para aumentar a reutilização.
- Utilizar Outputs para compartilhar informações entre Stacks.
- Testar alterações com Change Sets antes de atualizar ambientes produtivos.
- Aplicar o princípio do menor privilégio nas Roles IAM.
- Versionar todos os Templates em um repositório Git.
- Utilizar convenções de nomenclatura para facilitar a manutenção.
- Evitar alterações manuais em recursos gerenciados pelo CloudFormation (conhecidas como *drift*), pois podem causar inconsistências entre o Template e a infraestrutura real.

---

# Insights

## CloudFormation não executa aplicações

Ele apenas cria e gerencia infraestrutura.

Quem executa o código continua sendo serviços como:

- Lambda
- ECS
- EC2
- Batch

---

## CloudFormation é declarativo

O usuário descreve **o estado desejado** da infraestrutura.

Exemplo:

```
Quero:

1 Bucket

1 Lambda

1 IAM Role
```

O CloudFormation decide a ordem correta para criar os recursos.

---

## Excelente para CI/CD

Fluxo comum:

```
Git

↓

Pipeline

↓

CloudFormation

↓

Infraestrutura

↓

Deploy da aplicação
```

Isso garante ambientes consistentes em todas as etapas do desenvolvimento.

---

# Serviços frequentemente utilizados com CloudFormation

| Serviço | Utilização |
|----------|------------|
| S3 | Armazenamento de arquivos e hospedagem de templates |
| Lambda | Processamento serverless |
| IAM | Controle de permissões |
| CloudWatch | Logs e monitoramento |
| VPC | Redes privadas |
| EC2 | Máquinas virtuais |
| DynamoDB | Banco NoSQL |
| RDS | Banco de dados relacional |
| SNS | Notificações |
| SQS | Filas |
| EventBridge | Eventos |
| AWS WAF | Firewall para aplicações web |
| AWS Network Firewall | Firewall gerenciado para redes VPC |
| Security Groups | Firewall em nível de recurso |

---

# Resumo

- O AWS CloudFormation é o serviço de Infraestrutura como Código (IaC) da AWS.
- A infraestrutura é definida por Templates em YAML ou JSON.
- Uma **Stack** representa o conjunto de recursos criados por um Template.
- É possível criar, atualizar e excluir Stacks de forma automatizada.
- O serviço integra-se nativamente com praticamente todos os recursos da AWS.
- Recursos de segurança, como Security Groups, AWS WAF e AWS Network Firewall, também podem ser provisionados por meio de Templates.
- O CloudFormation promove automação, padronização, versionamento e reprodutibilidade da infraestrutura.
