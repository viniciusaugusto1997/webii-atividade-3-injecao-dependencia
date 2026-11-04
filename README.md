# Atividade 3 — Injeção de dependência

## Objetivo

Implementar injeção de dependência em `courses` e `enrollments`,
seguindo o módulo `students` e preservando o comportamento da API.

## Alterações

- Contratos `CourseRepository` e `EnrollmentRepository` para as operações existentes.
- Repositórios PostgreSQL recebem `Database` pelo construtor.
- Serviços recebem os contratos de repositório pelo construtor.
- Rotas recebem os serviços por parâmetro.
- `createApp(db)`, em `server/src/app.ts`, monta as dependências com o mesmo banco.

## Executar com Docker Compose

Com Docker e Compose disponíveis, execute na raiz pelo PowerShell:

```powershell
if (!(Test-Path .env)) { Copy-Item .env.example .env }
docker compose up -d --build
```

Interface: http://localhost:8080, com as portas do `.env.example`.
Para encerrar preservando os dados, execute `docker compose down`, sem `-v`.

## Verificar tipos e compilar

Com Node.js e npm instalados, execute a partir da raiz:

```powershell
cd server
npm.cmd ci
npm.cmd run typecheck
npm.cmd run build
```

A validação anterior utilizou PostgreSQL local. Docker Compose não foi testado.

## Créditos

Projeto original do professor Arley Souza:
[webII-2026-2-dependencia](https://github.com/arleysouza/webII-2026-2-dependencia).
