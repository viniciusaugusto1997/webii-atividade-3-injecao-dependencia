# Atividade 3 — Injeção de dependência

## Implementação

Os módulos `courses` e `enrollments` seguem o exemplo de `students`:

| Requisito | Evidência no código |
| --- | --- |
| Contratos com tipos de entrada e retorno | `CourseRepository` e `EnrollmentRepository`, nos respectivos arquivos `*.repository.ts`: `create`, `findAll` e `findOne`, retornos assíncronos e ausência representada por `undefined`. |
| Implementações PostgreSQL | `PgCourseRepository implements CourseRepository` e `PgEnrollmentRepository implements EnrollmentRepository`. |
| Banco fornecido aos repositórios | Construtores recebem `Database`; consultas usam `this.db.query`, sem importar `pool`. |
| Repositórios fornecidos aos serviços | Construtores de `CoursesService` e `EnrollmentsService` recebem os contratos, sem criar implementações concretas. |
| Serviços fornecidos às rotas | `coursesRoutes(service)` e `enrollmentsRoutes(service)`, sem criar serviços internamente. |
| Composição com o mesmo banco | `createApp(db)`, em `server/src/app.ts`, monta repositórios, serviços e rotas com o mesmo `db`. |
| Preservação do comportamento | Comparação com o original e testes HTTP com PostgreSQL real; SQL, validações e respostas preservados. |
| Preservação de alunos | Os três arquivos de `students` são idênticos aos do original. |
| Sem framework adicional | `package.json` e lockfile preservados. Imports de contratos são `import type`. |

A implementação preliminar já atendia aos requisitos de código. A revisão
confirmou as alterações nos sete arquivos previstos: `app.ts` e os arquivos de
repositório, serviço e rotas dos dois módulos. Foram corrigidos o `.gitignore`
e a documentação da entrega. O `.env` local foi preservado e não integra o Git.

Fonte: projeto de Arley Souza,
[webII-2026-2-dependencia](https://github.com/arleysouza/webII-2026-2-dependencia),
commit comparado `2d89c587ce8957262e8b746c7af252a13241d63b`.

## Validação executada em 03/11/2026

| Verificação | Resultado real |
| --- | --- |
| `npm.cmd ci --no-audit --no-fund`, em `server` | Aprovado: 95 pacotes instalados pelo lockfile, sem alterá-lo. |
| `npm.cmd run typecheck` | Aprovado. |
| `npm.cmd run build` | Aprovado. |
| PostgreSQL real | PostgreSQL 18.6, cluster temporário independente restrito a `127.0.0.1:55437`; sem utilizar o serviço PostgreSQL existente. |
| Schema e dados iniciais | `database/init.sql` e `database/seed.sql` executados com parada em erro: 3 alunos, 39 cursos e 22 matrículas iniciais. |
| HTTP | 46 verificações aprovadas com status HTTP e conteúdo JSON. |
| Persistência | Consulta SQL com JOIN confirmou aluno, curso e matrícula criados; leitura HTTP correta após reiniciar a aplicação. |
| Arquivos da interface | HTML, JavaScript, CSS e consulta de cursos por proxy temporário: 4 verificações aprovadas. |
| Efeitos colaterais de imports | Importar `app` compilado não carregou `database/connection.js` nem o pool global. |
| Docker/Compose e Nginx | Não executados: Docker não está instalado/no PATH, inclusive no caminho padrão do Docker Desktop. |
| Navegador | Renderização e interação visual não verificadas. O teste HTTP dos arquivos não substitui teste em navegador. |

Os 46 testes HTTP cobriram `/health`; listagem inicial e após cadastro;
criação e consulta de aluno, curso e matrícula; HTTP 400 para corpo, nome, RA,
IDs e parâmetros inválidos; HTTP 404 para registros e rota inexistentes e para
matrícula com aluno ou curso ausente; HTTP 409 para aluno, curso e matrícula
duplicados; e consulta após reinicialização da aplicação. Os erros tiveram
`statusCode`, `message` e `error` conferidos; os cadastros tiveram seus campos e
valores conferidos, incluindo a data da matrícula.

Os testes usaram exclusivamente dados identificados como
`Validacao Atividade 3 3520fc37` e `Validacao DI 3520fc37` no cluster temporário.
Scripts temporários, logs, banco, `node_modules`, `dist` e segredos não fazem
parte da entrega. O relato anterior sobre banco simulado foi substituído pelas
verificações reais acima.

## Executar

Com Node.js e npm instalados, no PowerShell:

```powershell
cd server
npm.cmd ci
npm.cmd run typecheck
npm.cmd run build
```

Para executar a aplicação completa, é necessário instalar/iniciar Docker com
Compose. Na raiz, preserve qualquer `.env` existente:

```powershell
if (!(Test-Path .env)) { Copy-Item .env.example .env }
docker compose up -d --build
docker compose ps
docker compose logs --tail 100
```

Com as portas do `.env.example`, abra a interface em http://localhost:8080 e
consulte http://localhost:3002/health. A API oferece `/api/students`,
`/api/courses` e `/api/enrollments`. O PostgreSQL fica em `localhost:5433`.
Se o `.env` local usar outras portas, use os valores locais.

O volume preserva os dados. Para encerrar, use `docker compose down`, sem `-v`.
O Compose não define `container_name` nem nome externo fixo para o volume,
mas define tags de imagens fixas; uma futura validação isolada precisa considerar
também imagens e portas. Não foram iniciados serviços Compose nesta validação.

## Situação da entrega

Implementação e validação da API com PostgreSQL real concluídas. A execução
completa por Docker/Compose e a interface em navegador permanecem pendentes.
O envio na plataforma acadêmica deve ser feito pessoalmente pelo aluno, usando
o link do repositório público informado no relatório final.

Revisão Git: o índice contém somente os 35 arquivos da entrega. A checagem
de espaços apontou ocorrências já existentes no original (linhas em branco
com espaços em rotas/serviço e ao final do Dockerfile do banco); foram
preservadas para evitar alterações alheias ao exercício. A busca por padrões
de tokens e chaves privadas não encontrou ocorrências nos arquivos da entrega.
