# 07 · Оплата и цены

> **По умолчанию:** **Superwall** на iOS/Android, **Stripe** в вебе, **одна таблица прав (entitlements)** в D1. Около **$30 в год с бесплатным пробным периодом**.
> **Агенту: читать, когда** делаешь пейволы, покупки, вебхуки или любую проверку «у этого пользователя есть Pro?».

## Воронка (средние оценки, а не цели)

```
downloads ──30%──▶ start a free trial ──30%──▶ pay $30/year
                 ≈ 9% of downloads become paying subscribers ≈ $2.70 per download
```

(Скачивания → 30% начинают бесплатный пробный период → 30% из них платят $30 в год. Платящими становятся ≈ 9% скачавших, это ≈ $2.70 со скачивания.)

Измеряй свои цифры ([12](12-costs.md)).

## Инфраструктура и комиссии (октябрь 2026)

| Платформа | Чем | Комиссия |
|---|---|---|
| iOS / Android | **[Superwall](https://superwall.com)**: удалённые пейволы и A/B-тесты | Бесплатно до $10k в месяц выручки с пейволов, потом 1%\* |
| Веб | **[Stripe](https://stripe.com)**: Checkout + Billing + Customer Portal | 2.9% + 30¢, плюс 0.7% за Billing |
| Веб-оплата для iOS | **[Superwall web checkout](https://superwall.com/docs/web-checkout)** (Stripe) | Как выше |
| **Apple** | Подписки | **15%** в [Small Business Program](https://developer.apple.com/app-store/small-business-program/) (≤ $1M в год). Иначе 30% в первый год, 15% потом |
| **Google Play** | Подписки | **10% + 5% за биллинг** в США, ЕЭЗ, Великобритании (с 30 июня 2026). 15% в остальных странах |

\*Страницы цен Superwall противоречили друг другу. Уточни на [superwall.com/pricing](https://superwall.com/pricing).

- **[HUMAN] Вступи в Small Business Program от Apple в первый же день.**
- Приложения в американском App Store могут давать ссылку на веб-оплату. Комиссия Apple с таких покупок всё ещё в суде (октябрь 2026). Проверь на практике.

## Цены

1. На первом месте годовая подписка, ~$30 в год, показанная за неделю («$0.58 в неделю»).
2. Максимум один короткий вариант (неделя или месяц).
3. Всегда бесплатный пробный период (3 или 7 дней) с напоминанием перед концом.
4. Жёсткий пейвол после момента «ага»: онбординг → персональный результат → пейвол. Любой бесплатный тариф ограничен в день.
5. Одно предложение при отказе: годовая со скидкой, показывается один раз.
6. Локализуй цены по странам магазина.
7. Тестируй цену, длину пробного периода и дизайн в Superwall.

## Права доступа (entitlements)

Таблицу `entitlements` ([03](03-database.md)) пишут **только** проверенные вебхуки:
- `POST /webhooks/stripe`: проверка через `stripe.webhooks.constructEventAsync`.
- `POST /webhooks/superwall`: события App Store и Play через Superwall. Проверяй подпись.

Записи идемпотентны (ключ — id события). Все клиенты вызывают `GET /me/entitlements`, поэтому покупка на любой платформе открывает доступ везде.

## Чек-лист пейвола (ревью App Store)

- [ ] Показаны цена, период, длина пробного периода, «продлевается автоматически до отмены»
- [ ] Ссылки на Terms (EULA) и Privacy
- [ ] **Restore Purchases**
- [ ] Видимая кнопка закрытия, никаких тёмных паттернов
- [ ] Напоминание о конце пробного периода запланировано
- [ ] `paywall_viewed`, `trial_started`, `subscription_started` отправляются с id варианта

## Ресурсы

- [Документация Superwall](https://superwall.com/docs) · [Галерея пейволов Superwall](https://superwall.com/applications) · [Блог Superwall](https://superwall.com/blog)
- [Stripe Billing](https://docs.stripe.com/billing) · [Вебхуки Stripe](https://docs.stripe.com/webhooks)
- [Подписки в App Store](https://developer.apple.com/app-store/subscriptions/)

---

Оригинал: [playbook/07-payments-pricing.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/07-payments-pricing.md) · MIT © Blake Anderson
