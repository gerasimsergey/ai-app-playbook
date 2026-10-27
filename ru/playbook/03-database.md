# 03 · База данных: D1 → PlanetScale

> **По умолчанию:** D1, чистый SQL, миграции Wrangler. **Путь роста:** PlanetScale Postgres через Hyperdrive. Без ORM.
> **Агенту: читать, когда** добавляешь таблицы, пишешь миграции или решаешь, пора ли переезжать.

## Где лежат данные

| Данные | Хранилище |
|---|---|
| Пользователи, подписки, записи приложения | **D1** |
| Живое состояние одного чата / комнаты / пользователя | **Durable Object** (SQLite) |
| Настройки, флаги, кэш чтения | **KV** |
| Фото, аудио, медиа | **R2** (ключ в D1) |
| Эмбеддинги | **Vectorize** |

## Чистый SQL

```ts
const meals = await env.DB
  .prepare("SELECT id, calories, created_at FROM meals WHERE user_id = ? ORDER BY created_at DESC LIMIT 50")
  .bind(user.id)
  .all();
```

- Всегда `prepare().bind()`. Никакого SQL, собранного из строк.
- `env.DB.batch([...])` для записи из нескольких запросов.
- Запросы лежат рядом с функцией (`meals.ts`), каждый — одна типизированная функция.

## Миграции

```bash
wrangler d1 migrations create DB add_meals           # creates migrations/000N_add_meals.sql
wrangler d1 migrations apply DB --local               # dev
wrangler d1 migrations apply DB --env staging --remote
wrangler d1 migrations apply DB --env production --remote   # humans only
```

(Последнюю команду, на боевой базе, запускает только человек.)

Каждое изменение схемы — миграция в git. Никогда не правь боевую базу руками.

## Стартовые таблицы

```sql
-- Better Auth creates: user, session, account, verification (see 04 · Auth)
CREATE TABLE entitlements (
  user_id TEXT NOT NULL, entitlement TEXT NOT NULL,         -- 'pro'
  source TEXT NOT NULL,                                     -- 'app_store' | 'play' | 'stripe'
  product_id TEXT, status TEXT NOT NULL,                    -- trialing | active | grace | expired | refunded
  current_period_end INTEGER, original_transaction_id TEXT,
  updated_at INTEGER NOT NULL,
  PRIMARY KEY (user_id, entitlement)
);
CREATE TABLE usage_ledger (                                 -- append-only: AI calls, credits
  id TEXT PRIMARY KEY, user_id TEXT NOT NULL, feature TEXT NOT NULL,
  units INTEGER NOT NULL, cost_micros INTEGER NOT NULL, created_at INTEGER NOT NULL
);
CREATE INDEX usage_user_day ON usage_ledger (user_id, created_at);
```

Таблицы user, session, account, verification создаёт Better Auth. `usage_ledger` — журнал только на дописывание (вызовы AI, кредиты).

Для id используй ULID или UUIDv7. Никогда не показывай наружу автоинкрементные id.

## Переезжай на PlanetScale, когда

- D1 близка к лимиту размера или записи встают в очередь.
- Нужны возможности Postgres: сложные join, JSONB, `pgvector`, тяжёлая аналитика.
- Нужны изменения схемы без простоя на больших таблицах или ветки базы на каждый PR.

Иначе оставайся на D1.

## Как переехать

1. **[HUMAN]** Создай базу [PlanetScale Postgres](https://planetscale.com/docs/postgres).
2. Поставь перед ней **[Hyperdrive](https://developers.cloudflare.com/hyperdrive/)**.
3. Делай запросы через [`postgres`](https://github.com/porsager/postgres) по `env.HYPERDRIVE.connectionString`, чистым SQL.
4. Перенеси миграции на Postgres; для staging используй ветки PlanetScale.
5. Пиши в обе базы, перенеси старые данные, сверь совпадение по каждой таблице, переключись. Держи D1, пока совпадение не доказано.

## Ресурсы

- [Документация D1](https://developers.cloudflare.com/d1/) · [Миграции D1](https://developers.cloudflare.com/d1/reference/migrations/) · [Лимиты D1](https://developers.cloudflare.com/d1/platform/limits/)
- [Документация PlanetScale](https://planetscale.com/docs) · [Hyperdrive + Postgres](https://developers.cloudflare.com/hyperdrive/examples/connect-to-postgres/)

---

Оригинал: [playbook/03-database.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/03-database.md) · MIT © Blake Anderson
