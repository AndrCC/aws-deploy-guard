# Architecture Notes

## Ideia central

Um workflow de deploy tenta adquirir um lock para um ambiente específico, por exemplo:

- repo: aws-deploy-guard
- environment: prod

Se o lock não existir, o deploy pode continuar.
Se o lock já existir, o deploy deve ser bloqueado.

## Fluxo inicial

1. Cliente chama `POST /lock/acquire`
2. A aplicação tenta criar um lock no DynamoDB
3. Se conseguir, retorna sucesso
4. Se não conseguir, retorna conflito
5. No final do deploy, cliente chama `POST /lock/release`

## Entidade de lock

- lockId
- repo
- environment
- runId
- commitSha
- createdAt
- expiresAt

## Regra principal

Só pode existir um lock ativo por combinação de `repo + environment`.