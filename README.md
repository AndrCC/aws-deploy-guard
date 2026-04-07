# aws-deploy-guard

Projeto de estudo inspirado em um caso real de falha operacional com deploys sobrepostos em ambiente com estado compartilhado.

## Problema

Deploys concorrentes podem causar comportamento incorreto em sistemas que dependem de um único escritor ou de acesso coordenado ao mesmo estado.

## Objetivo

Criar um guard simples de deploy na AWS para impedir que dois deploys do mesmo ambiente rodem ao mesmo tempo.

## MVP

- API para adquirir lock de deploy
- API para liberar lock de deploy
- Persistência do lock no DynamoDB
- Deploy com AWS SAM
- Integração futura com GitHub Actions

## Stack

- TypeScript
- Node.js
- AWS Lambda
- API Gateway
- DynamoDB
- AWS SAM

## Motivação

Transformar uma lição de confiabilidade operacional em um projeto pequeno, prático e transferível para cenários reais de produção.