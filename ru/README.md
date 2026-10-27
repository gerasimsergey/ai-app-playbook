> Перевод на русский книги [ai-app-playbook](https://github.com/blakeandersonw/ai-app-playbook) (Blake Anderson). Оригинал на английском, лицензия MIT. Перевод: 27.10.2026.
>
> MIT © Blake Anderson

```
Clone https://github.com/blakeandersonw/ai-app-playbook and read AGENTS.md
```

Вставь это в Claude Code или OpenCode в пустой папке.

[Подпишись на автора](https://github.com/blakeandersonw) и поставь звезду репозиторию, чтобы следить за обновлениями.

# Как делать полезные и прибыльные приложения в 2026 году

Обо мне: за последние 3 года я довёл 5 приложений до $100k+ MRR (выручки в месяц).

С этого стека я бы начинал сегодня. На нём мы изначально сделали [EcoGPT](https://ecogpt.com) ([вакансия](https://ecogpt.com/careers/founding-engineer)).

Хорошие приложения помогают людям прийти в форму, сосредоточиться, учиться или успокоиться, и за это люди платят. Эта инструкция ведёт от выбора проблемы до первого прибыльного месяца.

## Примеры

| Помогать людям… | Как |
|---|---|
| 💪 Прийти в форму и лучше питаться | Cal AI, Ladder, Yuka |
| 📵 Вернуть себе время | Opal, Structured |
| 📚 Учиться | Duolingo, Speak, Photomath |
| 🧘 Чувствовать себя спокойнее | Hallow, Finch |
| 🌱 Понимать мир вокруг | PictureThis, Yuka |
| 💼 Зарабатывать | SideShift |

## Путь

| Этап | Что | Проверка |
|---|---|---|
| 0 · Ниша | Выбери частую проблему, которую AI решает за секунды. Проверь её 10 видео и листом ожидания | Балл ≥ 25/35, реальный спрос |
| 1–12 · Сборка | Бриф, репо, бэкенд, вход, AI, веб, iOS/Android, оплата, аналитика, онбординг, удержание, запуск | «Готово, когда» для каждого этапа |
| 13 · Первая 1 000 | Короткие видео, авторы на SideShift / Trybe, личные сообщения инфлюенсерам и мем-пабликам | Контент вышел и отслеживается |
| 14 · Рост | Каждую неделю: один эксперимент с воронкой, одно исправление в продукте | Недельный отчёт |

## Стек

| Задача | Чем |
|---|---|
| Бэкенд, данные, файлы, фоновые задачи, почта | [Cloudflare](https://developers.cloudflare.com/workers/) (Workers, D1, R2, KV, Durable Objects, Queues, Workflows, AI Gateway) |
| Вход (auth) | [Better Auth](https://www.better-auth.com) |
| База данных на масштабе | [PlanetScale](https://planetscale.com) |
| AI | [OpenRouter](https://openrouter.ai), по умолчанию DeepSeek. Модели выбирай через [Artificial Analysis](https://artificialanalysis.ai) |
| Свой AI (распознавание речи, генерация картинок, свои LLM) | [Vast.ai](https://vast.ai) / [RunPod](https://www.runpod.io). На больших объёмах намного дешевле |
| Оплата | [Superwall](https://superwall.com) (мобильные), [Stripe](https://stripe.com) (веб) |
| Аналитика + ошибки | [Tinybird](https://www.tinybird.co) |
| Клиенты | SwiftUI для iOS, React Native или Kotlin для Android, React + Vite для веба |
| Разработка | [Claude Code](https://claude.com/claude-code) или [OpenCode](https://opencode.ai) + DeepSeek |

**Не используй:** Vercel, Netlify, Lovable, Bolt, v0, хостинг Replit, Bubble, FlutterFlow, Firebase, Supabase, Clerk, Auth0, Sentry, PostHog, Next.js, ORM.

## Деньги (в месяц)

Оценка по средним показателям: 30% скачавших начинают пробный период, 30% из них платят $30 в год. Это около $2.70 с одного скачивания.

| | | | | |
|---|---|---|---|---|
| **Скачивания** | 1 000 | 10 000 | 100 000 | 1 000 000 |
| **Выручка** | $2,700 | $27,000 | $270,000 | $2,700,000 |
| Superwall (1%) | $27 | $270 | $2,700 | $27,000 |
| Cloudflare | $5 | $20 | $190 | $2,300 |
| Tinybird | $0 | $50 | $275 | $2,500 |
| Обработка AI | $35 | $350 | $3,500 | $35,000 |

Подробности: [12 · Расходы](playbook/12-costs.md).

## Наш стек против популярного\*

На 1 000 пользователей в месяц.

| Задача | Наш | Цена | Популярный | Цена | Популярный дороже |
|---|---|---|---|---|---|
| Хостинг + API | Cloudflare Workers | $0.21 | Vercel Pro | $3.48 | **~17×** |
| База + файлы | D1 + R2 | $0.15 | Supabase Pro | $1.62 | **~11×** |
| Вход | Better Auth | $0 | Clerk | $10.25 | **∞×** |
| AI (60 вызовов на пользователя) | DeepSeek | $29.43 | OpenAI `gpt-6.1-sol` | $420.00 | **~14×** |
| Аналитика + ошибки | Tinybird | ~$1.45 | PostHog + Sentry | ~$4.97 | **~3×** |
| Почта | Cloudflare Email | $1.04 | Resend | $2.15 | **~2×** |

\*Оценка при 100 тыс. пользователей в месяц, одинаковое использование, прайс-листы на 26 октября 2026. [Источники](playbook/12-costs.md#наш-стек-против-популярного).

## Главы

| # | Глава | |
|---|---|---|
| 00 | [Найди свою нишу](playbook/00-find-your-niche.md) | Шаблоны, оценочная таблица, проверка, области |
| 01 | [Разработка с агентами](playbook/01-building-with-agents.md) | Среды для агентов, быстрая сборка, чего не использовать |
| 02 | [Бэкенд](playbook/02-backend-cloudflare.md) | Один Worker, без фреймворка |
| 03 | [База данных](playbook/03-database.md) | D1 на чистом SQL, потом PlanetScale |
| 04 | [Вход](playbook/04-auth.md) | Better Auth, сначала анонимно |
| 05 | [AI](playbook/05-ai.md) | DeepSeek, Artificial Analysis, Vast.ai / RunPod |
| 06 | [Клиенты](playbook/06-clients.md) | SwiftUI для iOS, React Native или Kotlin для Android, React + Vite для веба |
| 07 | [Оплата и цены](playbook/07-payments-pricing.md) | Superwall, Stripe, $30 в год + пробный период |
| 08 | [Аналитика и ошибки](playbook/08-analytics-observability.md) | Всё в Tinybird |
| 09 | [Дизайн и UX](playbook/09-design-ux.md) | Где найти интерфейс для повторения, онбординг |
| 10 | [Удержание](playbook/10-retention.md) | Пуши, серии (streaks), почта |
| 11 | [Продвижение](playbook/11-distribution.md) | Короткие видео, авторы, личные сообщения, ASO |
| 12 | [Расходы](playbook/12-costs.md) | Цифры и источники |
| 13 | [Безопасность и требования](playbook/13-security-privacy-compliance.md) | Лимиты трат, приватность, ревью App Store |

**Шаблоны (в оригинале):** [`worker.ts`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/worker.ts) · [`auth.ts`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/auth.ts) · [`wrangler.jsonc`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/wrangler.jsonc) · [`AGENTS.app.md`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/AGENTS.app.md) · [`dev.vars.example`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/dev.vars.example)

---

MIT © Blake Anderson
