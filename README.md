# Executando-Tarefas-Automatizadas-Lambda-e-S-
Lambda e S3
☁️ Infraestrutura Automatizada e Tarefas Serverless com AWS CloudFormation, Lambda e S3
🎯 Objetivo do Projeto

Este projeto tem como objetivo consolidar conhecimentos em automação de tarefas na AWS, utilizando CloudFormation, Lambda Function e Amazon S3, além de integrar DynamoDB, API Gateway e IAM para construir uma arquitetura serverless completa, segura e escalável.

O resultado final é um repositório com anotações, exemplos práticos e insights obtidos durante o laboratório — servindo como material de apoio para estudos e futuras implementações.

🧱 Parte 1 — Infraestrutura Automatizada com AWS CloudFormation

O AWS CloudFormation é o serviço que permite criar e gerenciar recursos da AWS automaticamente, a partir de templates escritos em YAML ou JSON.
Com ele, é possível provisionar servidores (EC2), bancos de dados (RDS), buckets (S3) e outras estruturas sem precisar configurar tudo manualmente.

🌩️ Benefícios

Criação rápida de ambientes padronizados;

Redução de erros manuais;

Controle de versão e reprodutibilidade;

Facilidade de auditoria e governança.

🪜 Etapas para criar uma Stack

Acesse o AWS CloudFormation pelo console;

Selecione Create Stack → With new resources;

Faça upload do template YAML/JSON;

Defina parâmetros e permissões;

Acompanhe a criação até o status CREATE_COMPLETE;

Consulte as saídas (Outputs) para ver resultados e acessos.

💡 Resumo

O CloudFormation é a base da infraestrutura como código (IaC) — uma forma de automatizar e documentar a infraestrutura, garantindo consistência e eficiência.

⚙️ Parte 2 — Executando Tarefas Automatizadas com AWS Lambda e Amazon S3
🧠 O que é o AWS Lambda

O Lambda é um serviço de computação sem servidor (serverless) que executa códigos sob demanda, sem precisar manter máquinas ligadas.
Ele é ativado por gatilhos (triggers), como o envio de um arquivo ao S3 ou uma requisição ao API Gateway.

🪣 O que é o Amazon S3

O Amazon S3 é o serviço de armazenamento de objetos da AWS.
É usado para guardar arquivos, relatórios, logs e backups, com alta durabilidade e integração nativa com outros serviços da nuvem.

🧩 Arquitetura da Solução
🔄 Fluxo Resumido

O usuário faz uma requisição HTTP via API Gateway;

O API Gateway aciona uma função Lambda;

A Lambda Function processa os dados:

Grava metadados no DynamoDB;

Armazena arquivos ou logs no S3;

Retorna uma resposta ao cliente via API Gateway;

O CloudWatch registra logs e métricas para monitoramento.

🧩 Componentes Utilizados
🧠 AWS Lambda

Executa o código de forma automática, sob demanda.

Responsável por processar dados, interagir com o DynamoDB e o S3.

Exemplo de Função Lambda (Python):

import json
import boto3
import os

dynamodb = boto3.resource('dynamodb')
s3 = boto3.client('s3')

TABLE_NAME = os.environ['TABLE_NAME']
BUCKET_NAME = os.environ['BUCKET_NAME']

def lambda_handler(event, context):
    data = json.loads(event['body'])
    item_id = data.get('id')
    content = data.get('content')

    # Salva metadado no DynamoDB
    table = dynamodb.Table(TABLE_NAME)
    table.put_item(Item={'id': item_id, 'content': content})

    # Armazena conteúdo no S3
    s3.put_object(Bucket=BUCKET_NAME, Key=f"{item_id}.txt", Body=content)

    return {
        'statusCode': 200,
        'body': json.dumps({'message': 'Processamento concluído!', 'id': item_id})
    }

🪣 Amazon S3

Armazena os arquivos processados ou gerados pela função Lambda.

Pode ser usado para logs, backups ou uploads feitos por usuários.

Exemplo:
A Lambda salva o arquivo dados/001.txt dentro do bucket meu-bucket-lambda-s3.

🧾 Amazon DynamoDB

Banco de dados NoSQL totalmente gerenciado.

Armazena metadados, status e timestamps das execuções.

Exemplo de tabela:

id	content	timestamp
001	"Teste de automação"	2025-10-29T15:00:00Z
🌐 Amazon API Gateway

Porta de entrada para as APIs.

Encaminha requisições HTTP para a Lambda.

Oferece autenticação, limites e controle de acesso.

Exemplo de endpoint:

POST https://xxxxxx.execute-api.us-east-1.amazonaws.com/dev/processar

🔐 AWS IAM

Controla identidade e permissões de acesso.

Define o que cada serviço ou usuário pode fazer.

Exemplo de política de acesso da Lambda:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:PutObject", "s3:GetObject"],
      "Resource": "arn:aws:s3:::meu-bucket-lambda-s3/*"
    },
    {
      "Effect": "Allow",
      "Action": "dynamodb:PutItem",
      "Resource": "arn:aws:dynamodb:us-east-1:123456789012:table/MinhaTabela"
    }
  ]
}

📘 Conceitos-Chave
Serviço	Conceito	Benefício
Lambda	Executa código sob demanda.	Custo por uso e alta escalabilidade.
S3	Armazenamento de objetos.	Seguro, durável e integrado.
DynamoDB	Banco de dados NoSQL.	Alta disponibilidade e performance previsível.
API Gateway	Exposição de endpoints.	Segurança e controle de requisições.
IAM	Controle de acesso.	Garante o princípio do menor privilégio.
💡 Insights e Aprendizados

A combinação de Lambda + API Gateway + DynamoDB + S3 cria sistemas totalmente serverless, eliminando a necessidade de servidores dedicados.

O uso do IAM garante segurança e controle de acesso refinado.

O CloudWatch é essencial para acompanhar logs e métricas das execuções.

Essa arquitetura reduz custos, aumenta a escalabilidade e simplifica o gerenciamento.
