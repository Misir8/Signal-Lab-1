# Signal Lab — Submission Checklist

Заполни этот файл перед сдачей. Он поможет интервьюеру быстро проверить решение.

---

## Репозиторий

- **URL**: [`signal-lab`](https://github.com/Misir8/signal-lab)
- **Ветка**: `main`
- **Время работы** (приблизительно): `6` часов

---

## Запуск

```bash
# Команда запуска:
docker compose up -d

# Команда проверки:
docker compose ps && curl -s http://localhost:3001/api/v1/core/health

# Команда остановки:
docker compose down
```

**Предусловия**: (Docker version, Node version, что ещё нужно)

# Обязательно:
- Docker: 20.10+ (проверить: docker --version)
- Docker Compose: 2.0+ (проверить: docker compose version)
- Git: 2.0+ (проверить: git --version)

# Для локальной разработки (опционально):
- Node.js: 18+ (проверить: node --version)
- Yarn: 1.22+ (проверить: yarn --version)

# Порты должны быть свободны:
- 3000 (Frontend)
- 3001 (Backend API)
- 3100 (Grafana)
- 3101 (Loki)
- 5432 (PostgreSQL)
- 9090 (Prometheus)

# Проверить свободные порты:
lsof -i :3000
lsof -i :3001
lsof -i :3100
lsof -i :5432
lsof -i :9090

# Минимальные системные требования:
- RAM: 4GB свободно
- Disk: 2GB свободно
- OS: macOS, Linux, Windows (с WSL2)


## Стек — подтверждение использования

| Технология | Используется? | Где посмотреть |
|-----------|:------------:|----------------|
| Next.js (App Router) | ☑ | `apps/web/next.config.ts`, `apps/web/src/app/` |
| shadcn/ui | ☑ | `apps/web/components.json`, `apps/web/src/components/ui/` |
| Tailwind CSS | ☑ | `apps/web/postcss.config.mjs`, `apps/web/src/app/globals.css` |
| TanStack Query | ☑ | `apps/web/package.json` (dependencies), `apps/web/src/components/providers.tsx` |
| React Hook Form | ☑ | `apps/web/package.json` (dependencies), `apps/web/src/components/scenario-runner.tsx` |
| NestJS | ☑ | `nest-cli.json`, `apps/core/src/main.ts`, `apps/core/src/core.module.ts` |
| PostgreSQL | ☑ | `docker-compose.yml` (postgres service), `prisma/schema.prisma` (datasource) |
| Prisma | ☑ | `prisma/schema.prisma`, `prisma/migrations/`, `libs/prisma/` |
| Sentry | ☑ | `apps/bootstrap.ts` (Sentry.init), `apps/core/src/scenarios/services/scenario.service.ts` (captureException) |
| Prometheus | ☑ | `docker-compose.yml` (prometheus service), `observability/prometheus/prometheus.yml` |
| Grafana | ☑ | `docker-compose.yml` (grafana service), `observability/grafana/dashboards/` |
| Loki | ☑ | `docker-compose.yml` (loki service), `observability/loki/loki-config.yml` |


---

## Observability Verification

Опиши, как интервьюер может проверить каждый сигнал:

| Сигнал | Как воспроизвести | Где посмотреть результат |
|--------|-------------------|--------------------------|
| Prometheus metric | 1. Открыть UI: `http://localhost:3000`<br>2. Нажать "Run Scenario: success"<br>3. Подождать 2-3 секунды | `http://localhost:3001/api/v1/core/metrics`<br>Искать: `scenario_runs_total{type="success"}` должен увеличиться<br>Или в Prometheus: `http://localhost:9090` → Query: `scenario_runs_total` |
| Grafana dashboard | 1. Запустить несколько сценариев из UI<br>2. Открыть Grafana: `http://localhost:3100`<br>3. Login: `admin` / `admin` | Dashboard: "Signal Lab Overview"<br>Должны обновиться панели:<br>- Scenario Execution Rate<br>- Total Scenarios<br>- Duration (p95/p50)<br>- Error Rate |
| Loki log | 1. Запустить любой сценарий из UI<br>2. Открыть Grafana: `http://localhost:3100`<br>3. Перейти в Explore → Loki | Query: `{app="signal-lab"}`<br>Должны появиться логи:<br>- "Scenario started"<br>- "Scenario completed"<br>Фильтр по типу: `{app="signal-lab"} |= "system_error"` |
| Sentry exception | 1. Открыть UI: `http://localhost:3000`<br>2. Нажать "Run Scenario: system_error"<br>3. Подождать выполнения | Открыть ваш Sentry проект<br>В Issues должна появиться ошибка:<br>- Title: "Simulated system error for observability testing"<br>- Tags: `scenarioId`, `type: system_error`<br>- Extra: `duration`, `metadata`<br>**Note**: Требуется настроить `SENTRY_DSN` в `.env` |


## Cursor AI Layer

### Custom Skills

| # | Skill name | Назначение |
|---|-----------|-----------|
| 1 | observability-skill | Добавление observability (logs, metrics, Sentry) к новым features |
| 2 | nestjs-module-skill | Создание NestJS модуля (controller, service, repository, DTOs) |
| 3 | react-component-skill | Создание React компонентов с TanStack Query и React Hook Form |
| 4 | prisma-schema-skill | Безопасное изменение Prisma schema и создание миграций |
| 5 | observability-review-skill | Review кода на соответствие observability требованиям |
| 6 | signal-lab-orchestrator | Выполнение PRD через 7-phase pipeline с оптимизацией моделей |

### Commands

| # | Command | Что делает |
|---|---------|-----------|
| 1 | add-endpoint | Добавляет новый API endpoint с observability (logs, metrics, Swagger) |
| 2 | check-obs | Проверяет observability для feature (logging, metrics, Sentry, dashboard) |
| 3 | health-check | Проверяет health всего Docker stack (containers, services, connectivity) |
| 4 | debug-observability | Отлаживает проблемы с observability (logs, metrics, errors) |
| 5 | dev-setup | Настраивает dev окружение с нуля (dependencies, env, migrations) |
| 6 | run-prd | Выполняет PRD простым способом (без orchestrator) |
| 7 | orchestrate-prd | Выполняет PRD через 7-phase pipeline с state persistence |


### Hooks

| # | Hook | Какую проблему решает |
|---|------|----------------------|
| 1 | check-prisma-schema.js | Напоминает запустить миграцию и regenerate Prisma Client после изменения schema |
| 2 | check-observability.js | Проверяет новые service/controller файлы на наличие observability (logs, metrics, Sentry) |
| 3 | check-dangerous-commands.js | Блокирует опасные shell команды (`rm -rf /`, fork bombs) перед выполнением |
| 4 | session-summary.js | Создает сводку по завершению agent session с next steps |


### Rules

| # | Rule file | Что фиксирует |
|---|----------|---------------|
| 1 | code-conventions.md | Naming conventions, file structure, NestJS/React patterns |
| 2 | observability-guardrails.md | Обязательные требования к logging, metrics, error tracking |
| 3 | stack-constraints.md | Запрещенные и разрешенные технологии (no Redux, no SWR, etc.) |
| 4 | prisma-patterns.md | Паттерны работы с Prisma (repository pattern, no raw SQL) |
| 5 | frontend-patterns.md | React/Next.js паттерны (TanStack Query, React Hook Form, shadcn/ui) |
| 6 | error-handling.md | Обработка ошибок на backend (custom errors) и frontend (error boundaries) |


### Marketplace Skills

| # | Skill | Зачем подключён |
|---|-------|----------------|
| 1 | | |
| 2 | | |
| 3 | | |
| 4 | | |
| 5 | | |
| 6 | | |

**Что закрыли custom skills, чего нет в marketplace:**

---

## Orchestrator

- **Путь к skill**: `.cursor/skills/signal-lab-orchestrator/SKILL.md`
- **Путь к context file** (пример): `.execution/2026-04-22-15-30/context.json`
- **Сколько фаз**: `7` (Analysis → Codebase Scan → Planning → Decomposition → Implementation → Review → Report)
- **Какие задачи для fast model**: 
  - Add field to Prisma schema
  - Create DTO with validation
  - Create simple endpoint
  - Add metric or log statement
  - Create UI component without complex logic
  - Add observability to existing code
- **Поддерживает resume**: да (через `@orchestrate-prd --resume .execution/<id>`)

## Скриншоты / видео

- [ ] UI приложения
- [ ] Grafana dashboard с данными
- [ ] Loki logs
- [ ] Sentry error

(Приложи файлы или ссылки ниже)

---

## Что не успел и что сделал бы первым при +4 часах

---

## Вопросы для защиты (подготовься)

1. Почему именно такая декомпозиция skills?
2. Какие задачи подходят для малой модели и почему?
3. Какие marketplace skills подключил, а какие заменил custom — и почему?
4. Какие hooks реально снижают ошибки в повседневной работе?
5. Как orchestrator экономит контекст по сравнению с одним большим промптом?
