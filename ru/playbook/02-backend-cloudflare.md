# 02 · Бэкенд: всё на Cloudflare

> **По умолчанию:** один аккаунт Cloudflare для API, данных, файлов, фоновых задач, почты, маршрутизации AI и веба. Обработчик `fetch` в Workers, без фреймворка.
> **Агенту: читать, когда** создаёшь или меняешь что-то на сервере.

Один аккаунт, одна CLI ([Wrangler](https://developers.cloudflare.com/workers/wrangler/)), один счёт.  в месяц хватает на настоящее приложение; у R2 нет платы за исходящий трафик ([12 · Расходы](12-costs.md)).

## Строительные блоки

| Нужно | Чем |
|---|---|
| HTTP API | **[Workers](https://developers.cloudflare.com/workers/)**, один обработчик `fetch` ([`templates/worker.ts`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/worker.ts)) |
| Реляционные данные | **[D1](https://developers.cloudflare.com/d1/)**, позже PlanetScale ([03](03-database.md)) |
| Состояние на пользователя / на чат, реалтайм | **[Durable Objects](https://developers.cloudflare.com/durable-objects/)** (SQLite) |
| Многошаговые AI-конвейеры | **[Workflows](https://developers.cloudflare.com/workflows/)** |
| Фоновые задачи | **[Queues](https://developers.cloudflare.com/queues/)** |
| Задачи по расписанию | **[Cron Triggers](https://developers.cloudflare.com/workers/configuration/cron-triggers/)** |
| Файлы, фото, аудио | **[R2](https://developers.cloudflare.com/r2/)** |
| Флаги, настройки, кэш | **[KV](https://developers.cloudflare.com/kv/)** |
| Эмбеддинги / память | **[Vectorize](https://developers.cloudflare.com/vectorize/)** |
| Маршрутизация AI, кэш, логи | **[AI Gateway](https://developers.cloudflare.com/ai-gateway/)** → OpenRouter ([05](05-ai.md)) |
| Вход | **Better Auth** в Worker ([04](04-auth.md)) |
| Почта | **[Email Service](https://developers.cloudflare.com/email-service/)** |
| Защита от ботов | **[Turnstile](https://developers.cloudflare.com/turnstile/)** |
| Лимиты запросов | **[Rate Limiting binding](https://developers.cloudflare.com/workers/runtime-apis/bindings/rate-limit/)** |
| Хостинг сайта | **[Workers static assets](https://developers.cloudflare.com/workers/static-assets/)** |

## Структура репозитория

```
apps/
  api/        Worker: src/index.ts (routes), auth.ts, ai.ts, billing.ts, events.ts; migrations/*.sql
  web/        React + Vite, deployed as static assets
  ios/        SwiftUI
  android/    Kotlin/Compose or React Native
docs/         brief.md, progress.md, decisions/
```

(api — Worker с маршрутами и миграциями; web — React + Vite как статика; ios — SwiftUI; android — Kotlin/Compose или React Native; docs — бриф, прогресс, решения.)

Начни с [`templates/worker.ts`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/worker.ts) и [`templates/wrangler.jsonc`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/wrangler.jsonc).

## Маршрутизация

```ts
const route = `${request.method} ${url.pathname}`;
switch (route) {
  case "GET /health": return Response.json({ ok: true });
  case "POST /ai/scan": return scan(request, env, user);
}
```

Для параметров в пути вроде `/items/:id` используй [`URLPattern`](https://developer.mozilla.org/docs/Web/API/URLPattern).

## Правила

1. Каждый ответ AI отдавай потоком (SSE или WebSocket).
2. Workers не хранят состояние. Состояние живёт в D1, Durable Objects, KV или R2.
3. Раздельные `env.staging` и `env.production`, у каждого свои ресурсы и секреты.
4. Секреты через `wrangler secret put`; локальные — в `.dev.vars` из gitignore.
5. Каждый промис либо await, либо `ctx.waitUntil()`.
6. Проверяй подпись каждого вебхука (Stripe, Superwall/App Store).
7. Задачи должны быть идемпотентными: Queues и Workflows повторяют попытки.
8. Вся серверная логика работает на Cloudflare. Никаких серверов сбоку.
9. Тяжёлая работа процессора идёт в Workflows или Queues, а не в запрос.
10. KV согласован в конечном счёте. Для счётчиков, кредитов и квот используй Durable Objects или D1.
11. Запись в D1 идёт в один регион на базу. Перерос это — сигнал переезжать ([03](03-database.md)).

## Ресурсы

- [Документация Workers](https://developers.cloudflare.com/workers/) · [Лучшие практики Workers](https://developers.cloudflare.com/workers/best-practices/workers-best-practices/) · [Настройка Wrangler](https://developers.cloudflare.com/workers/wrangler/configuration/)
- [Durable Objects](https://developers.cloudflare.com/durable-objects/) · [Workflows](https://developers.cloudflare.com/workflows/) · [Agents SDK](https://developers.cloudflare.com/agents/)
- [Цены Workers](https://developers.cloudflare.com/workers/platform/pricing/)

---

Оригинал: [playbook/02-backend-cloudflare.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/02-backend-cloudflare.md) · MIT © Blake Anderson
