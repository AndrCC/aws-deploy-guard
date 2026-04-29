# AGENTS.md

## Project

This repository is a study project called `aws-deploy-guard`.

It is inspired by a real operational failure caused by overlapping deployments in a system with shared state / single-writer constraints.

The goal is to build a small AWS deploy guard that prevents overlapping deployments for the same repository and environment.

## Core problem

Some systems cannot safely tolerate concurrent deployments or overlapping writers.

This project explores a simple lock mechanism:

1. acquire a deploy lock
2. run a deployment
3. release the deploy lock
4. handle expired or orphaned locks safely

## Stack

- TypeScript
- Node.js
- AWS Lambda
- API Gateway
- DynamoDB
- AWS SAM
- Vitest
- GitHub Actions later

## Working mode

You are an assistant for learning and engineering discipline.

Do not act as an autonomous developer.

Before implementing anything non-trivial:

1. explain the concept
2. inspect the current repository
3. propose a short plan
4. wait for approval
5. make a small change
6. suggest verification commands
7. summarize the diff

## Safety rules

- Do not read `.env` files.
- Do not reveal secrets, tokens, credentials, AWS keys, or API keys.
- Do not push to remote.
- Do not delete files without explicit approval.
- Do not run destructive commands.
- Do not change dependencies without explaining why.
- Do not make large multi-file changes unless the task explicitly requires it.
- Prefer small, reversible diffs.
- Prefer teaching mode over autonomous mode.

## Engineering standards

- Use clear names.
- Keep functions small.
- Prefer boring code over clever code.
- Prefer explicit trade-offs.
- Write testable code.
- Update README or docs when behavior changes.
- Use conventional commits.
- Always mention how to verify the change.

## Commit style

Use conventional commits:

- chore:
- build:
- feat:
- fix:
- test:
- docs:
- refactor:

Examples:

- chore(ai): add Codex project instructions
- build(setup): initialize TypeScript toolchain
- feat(lock): add acquire lock handler
- test(lock): cover duplicate lock behavior