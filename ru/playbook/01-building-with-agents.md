# 01 · Разработка с кодинг-агентами

> **По умолчанию:** Claude Code (подписка) для сложной работы. OpenCode + DeepSeek для объёма.
> **Агенту: читать, когда** настраиваешь репозиторий, цикл разработки или параллельную работу.

## Среда (harness)

| Задача | Среда |
|---|---|
| Архитектура, трудные баги, новые функции, рискованная работа | **[Claude Code](https://claude.com/claude-code)** на плане Max |
| Рефакторинг, тесты, миграции, тексты, переводы | **[OpenCode](https://opencode.ai)** + **DeepSeek** |

## Стек

| Задача | Чем |
|---|---|
| Сервер | **Cloudflare**: Workers, D1, R2, KV, Durable Objects, Queues, Workflows, AI Gateway, Email |
| Вход | **Better Auth** (библиотека в твоём Worker) |
| База на масштабе | **PlanetScale** |
| AI | **OpenRouter** (по умолчанию DeepSeek), модели выбираются через **Artificial Analysis** |
| GPU-процессы на объёме | **Vast.ai** / **RunPod** |
| Оплата | **Superwall** (мобильные) · **Stripe** (веб) |
| Аналитика + ошибки | **Tinybird** |
| Клиенты | **SwiftUI** для iOS · **React Native** или **Kotlin** для Android · **React + Vite** для веба |

Чистые API платформы: обработчик `fetch` в Workers, SQL, CSS-переменные. Без фреймворка маршрутизации, ORM и UI-кита.

## Не используй

**Vercel · Netlify · Lovable · Bolt · v0 · Replit (хостинг) · Bubble · FlutterFlow · Firebase · Supabase · Heroku · Render · Railway · AWS Amplify · Clerk · Auth0 · Sentry · PostHog · Next.js · Hono · Drizzle / Prisma**

Решай задачу стеком. Если это невозможно, спроси человека.

## Настройка репозитория

1. `AGENTS.md` в корне: стек, команды, правила, список «никогда». `CLAUDE.md` содержит `@AGENTS.md`. Начни с [`templates/AGENTS.app.md`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/AGENTS.app.md).
2. Циклы одной командой: `bun dev`, `bun test`, `bun run check`, `bun run deploy:staging`.
3. `docs/decisions/`: по одному короткому файлу на решение.
4. Маленькие файлы с понятными именами: `auth.ts`, `billing.ts`, `ai.ts`, `events.ts`.

## Делай быстро

1. День 1: основной цикл (один ввод → один результат) целиком на настоящем телефоне.
2. Подделывай ответ AI, пользователя и пейвол, пока экраны не станут правильными.
3. Копируй сценарии с образца ([09](09-design-ux.md)).
4. Начинай с [`templates/`](https://github.com/blakeandersonw/ai-app-playbook/tree/main/templates).
5. Быстрые циклы: превью SwiftUI, `wrangler dev` + горячая перезагрузка Vite, `curl`.
6. Параллельные агенты на разных частях (API, iOS, тексты), каждый в своём worktree.
7. Каждый день выкладывай в TestFlight. Каждый вечер пользуйся приложением сам.
8. Идеи, не нужные для запуска, записывай в `docs/later.md`.
9. Никаких абстракций до третьей копии.
10. Сроки:
    - Неделя 1: основной цикл и бэкенд.
    - Неделя 2: онбординг, пейвол, аналитика.
    - Неделя 3: полировка и материалы для магазина.
    - Неделя 4: запуск.

## Цикл разработки

- Проверка типов и тесты за секунды.
- Запусти приложение и посмотри экран, который менял.
- Одна задача на ветку. В PR написано, как проверено.

## Параллельные агенты

- Одна задача, одна ветка, один git worktree на агента (`git worktree add ../wt/feature-x -b feature-x`).
- Два агента никогда не работают с одними файлами.
- Человек проверяет и мёржит.

## Как ставить задачу

- Цель плюс «готово, когда»: «Пользователи могут восстановить покупки на iOS. Готово, когда кнопка Restore работает в песочнице и `entitlements` обновляется».
- Указывай на файлы и документацию, а не вставляй их.
- Называй ограничения: «без новых зависимостей», «только Cloudflare», «используй существующие токены».

## Ресурсы

- [Документация Claude Code](https://docs.claude.com/en/docs/claude-code/overview) · [Лучшие практики Claude Code](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Документация OpenCode](https://opencode.ai/docs) · [agents.md](https://agents.md)
- [Индекс документации Cloudflare для агентов (llms.txt)](https://developers.cloudflare.com/llms.txt)

---

Оригинал: [playbook/01-building-with-agents.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/01-building-with-agents.md) · MIT © Blake Anderson
