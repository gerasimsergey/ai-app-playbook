# 08 · Аналитика и ошибки: всё в Tinybird

> **По умолчанию:** **[Tinybird](https://www.tinybird.co)** для событий, выручки, стоимости AI и ошибок. Workers Observability для сырых логов сервера. Без отдельного трекера ошибок и инструмента аналитики.
> **Агенту: читать, когда** добавляешь функцию (у каждой функции есть событие) или разбираешься с продом.

## Конвейер

```
iOS / Android / Web ──▶ POST /events (Worker) ──▶ Queue ──▶ Tinybird Events API ──▶ SQL pipes ──▶ dashboards + alerts
Worker errors ───────────────────────────────────┘
```

(Клиенты → `POST /events` в Worker → очередь → Events API Tinybird → SQL-пайпы → дашборды и оповещения. Ошибки самого Worker идут в ту же очередь.)

- Клиенты шлют события и ошибки в твой Worker: одна схема, дополнение данными на сервере (id пользователя, тариф, страна, версия приложения), блокировщики рекламы ничего не съедают.
- Worker собирает пачки через **Queues** и отправляет NDJSON в [Events API](https://www.tinybird.co/docs/get-data-in/ingest-apis/events-api) токеном только на запись.
- Вариант на своём сервере: направь тот же конвейер в [ClickHouse](https://clickhouse.com/docs).

## События

```
app_opened              {platform, app_version}
onboarding_step         {step, index}
onboarding_completed
paywall_viewed          {placement, variant}
trial_started           {product_id, price}
subscription_started    {product_id, price, store}
subscription_renewed / subscription_cancelled / refunded
core_action             {feature}            ← the thing your app is for
ai_call                 {feature, model, tokens_in, tokens_out, latency_ms, cost_usd, ok}
share_tapped / referral_sent / review_prompted
error                   {source: api|ios|android|web, message, stack, route, app_version}
```

(`core_action` — то, ради чего существует твоё приложение.)

`snake_case`, одинаковые имена везде. Каждая видимая пользователю функция выходит вместе со своим событием.

## Ошибки

- **Worker:** `try/catch` вокруг `fetch`; событие `error` отправляется через `ctx.waitUntil()` ([`templates/worker.ts`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/worker.ts)).
- **Клиенты:** глобальный обработчик шлёт в `/events` пачками, без сети — повтор при следующем запуске. Включи отчёты о падениях в App Store Connect и Play Console.
- **Оповещения:** Cron Trigger каждые несколько минут опрашивает Tinybird (доля ошибок, сбои вебхуков, траты на AI в час) и шлёт письмо при превышении порога.

## Ежедневные дашборды

1. Воронка: скачивание → онбординг пройден → пейвол показан → пробный период → оплата.
2. Выручка: новые, возвраты, продления, по вариантам пейвола.
3. Удержание: D1 / D7 / D30 по когортам установки.
4. AI: стоимость на подписчика, стоимость на функцию, задержка p95, ошибки по моделям.
5. Здоровье: доля ошибок по источнику и версии приложения.

## Ресурсы

- [Документация Tinybird](https://www.tinybird.co/docs) · [Events API](https://www.tinybird.co/docs/get-data-in/ingest-apis/events-api) · [Документация ClickHouse](https://clickhouse.com/docs)
- [Workers Observability](https://developers.cloudflare.com/workers/observability/) · [Queues](https://developers.cloudflare.com/queues/)

---

Оригинал: [playbook/08-analytics-observability.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/08-analytics-observability.md) · MIT © Blake Anderson
