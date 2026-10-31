# 06 · Клиенты: iOS, Android, веб

> **По умолчанию:** SwiftUI для iOS, React Native или Kotlin для Android, React + Vite для веба.
> **Агенту: читать, когда** делаешь или меняешь любой клиент.

## Порядок

- Потребительские AI-приложения (камера, привычки, коучинг, вера, учёба): сначала iOS.
- Инструменты, которые находят через Google: сначала веб.
- Android вторым, если только твой рынок не в основном на Android.

## iOS: SwiftUI

- SwiftUI, `@Observable`, `async/await`. Никаких сторонних UI-библиотек.
- SF Pro, тактильный отклик (`sensoryFeedback`), пружинные анимации, шторки с detents, свайп назад.
- Виджеты, Live Activities, App Intents, Sign in with Apple.
- Оплата: Superwall SDK ([07](07-payments-pricing.md)).
- Сеть: один тип `API` на `URLSession` + `Codable`, bearer-токен из Keychain.
- TestFlight для каждой сборки.

[Уроки SwiftUI](https://developer.apple.com/tutorials/swiftui) · [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) · [Видео WWDC](https://developer.apple.com/videos/) · [Hacking with Swift](https://www.hackingwithswift.com)

## Android

| Выбирай | Когда |
|---|---|
| **React Native** | Нужно выпустить быстро или делить код с вебом |
| **Kotlin + Jetpack Compose** | Нужно нативное качество |

Superwall для Play Billing; Credential Manager для входа через Google.

[Документация Compose](https://developer.android.com/compose) · [Material 3](https://m3.material.io) · [Документация React Native](https://reactnative.dev)

## Веб: React + Vite на Cloudflare

- [Статика на Workers](https://developers.cloudflare.com/workers/static-assets/) через [плагин Cloudflare для Vite](https://developers.cloudflare.com/workers/vite-plugin/). API и сайт выходят одним деплоем.
- Чистый CSS с токенами в CSS-переменных, `fetch`, History API или один маленький роутер. Никакого UI-кита и библиотеки состояния, пока не можешь назвать проблему, которую они решат.
- Лендинг и SEO-страницы пререндери в статичный HTML.
- Никакого Next.js, никакого Vercel ([01](01-building-with-agents.md)).

## Все клиенты

1. Один API: тот же Worker.
2. Одна проверка прав: `GET /me/entitlements`. Клиенты никогда сами не решают, есть ли Pro.
3. Одна дизайн-система ([09](09-design-ux.md)).
4. Одна схема событий, отправляется в `POST /events` ([08](08-analytics-observability.md)).
5. Никаких API-ключей в бинарниках приложения.
6. Удалённые флаги (KV), чтобы выключать функции без ревью App Store.
7. API возвращает минимальную поддерживаемую версию приложения, чтобы заставить старые сборки обновиться.

---

Оригинал: [playbook/06-clients.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/06-clients.md) · MIT © Blake Anderson
