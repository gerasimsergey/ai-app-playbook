# 04 · Вход: Better Auth на Cloudflare

> **По умолчанию:** [Better Auth](https://www.better-auth.com) — библиотека в твоём Worker, хранит пользователей в D1 (`database: env.DB`). Сначала анонимно, потом Apple, Google или ссылка на почту.
> **Агенту: читать, когда** делаешь вход, сессии, привязку аккаунтов или удаление аккаунта.

## Плагины

| Функция | Чем |
|---|---|
| [`anonymous`](https://www.better-auth.com/docs/plugins/anonymous) | Пользоваться приложением до регистрации; данные переносятся при привязке |
| [Apple](https://www.better-auth.com/docs/authentication/apple), нативный `idToken` | Sign in with Apple в одно касание на iOS |
| [Google](https://www.better-auth.com/docs/authentication/google) `idToken` | Android и веб |
| [`magicLink`](https://www.better-auth.com/docs/plugins/magic-link) | Вход по почте без пароля через Cloudflare Email Service |
| [`bearer`](https://www.better-auth.com/docs/plugins/bearer) | Сессии по токену для нативных приложений (веб использует cookies) |
| `user.deleteUser` | Удаление аккаунта в приложении (Apple этого требует) |

## Настройка

1. `bun add better-auth`
2. Скопируй [`templates/auth.ts`](https://github.com/blakeandersonw/ai-app-playbook/blob/main/templates/auth.ts) в `apps/api/src/auth.ts`.
3. Таблицы входа как миграция D1: запусти `npx @better-auth/cli generate` на временном локальном конфиге SQLite, чтобы получить SQL для `user`, `session`, `account`, `verification`; сохрани как `migrations/0001_auth.sql`; примени через `wrangler d1 migrations apply`. Или один раз вызови `getMigrations` из `better-auth/db/migration` с защищённого маршрута на staging. ([документация](https://www.better-auth.com/docs/concepts/database))
4. Подключи маршрут:
   ```ts
   if (url.pathname.startsWith("/api/auth/")) return auth.handler(request);
   const session = await auth.api.getSession({ headers: request.headers });
   ```
5. Секреты: `wrangler secret put BETTER_AUTH_SECRET` (`openssl rand -base64 32`), плюс секреты клиентов Apple и Google.
6. Включи `nodejs_compat` (нужен для `AsyncLocalStorage`); включён по умолчанию с compatibility date 2026-08-04 и позже.

## Сценарии

**Веб:** сессии на cookies через `createAuthClient` из `better-auth/react`.

**iOS, Sign in with Apple:**
```
1. First launch → POST /api/auth/sign-in/anonymous → save the `set-auth-token` header in the Keychain
2. "Sign in with Apple" (AuthenticationServices) → identityToken + your raw nonce
3. POST /api/auth/sign-in/social   (Authorization: Bearer <anonymous token>)
   { "provider": "apple", "idToken": { "token": "<identityToken>", "nonce": "<rawNonce>" } }
4. Save the new `set-auth-token` and send `Authorization: Bearer <token>` on every request
```
По шагам: (1) при первом запуске анонимный вход, заголовок `set-auth-token` сохраняется в Keychain; (2) Sign in with Apple даёт identityToken и твой исходный nonce; (3) запрос на `/api/auth/sign-in/social` с анонимным токеном; (4) сохраняешь новый `set-auth-token` и шлёшь его в каждом запросе.

Укажи `appBundleIdentifier` в настройках Apple, иначе нативные токены не пройдут проверку `aud`.

**Android:** Credential Manager → Google ID token → `signIn.social({ provider: "google", idToken })`, затем bearer.

## Правила

1. Сначала анонимно. Аккаунт проси на пейволе или для синхронизации. Данные переноси в `onLinkAccount`.
2. На iOS предлагай Sign in with Apple везде, где предлагаешь Google (правило 4.8).
3. Удаление аккаунта в приложении (правило 5.1.1(v)). `beforeDelete` удаляет строки в D1 и файлы в R2 и отзывает токены Apple.
4. Токены в Keychain или Keystore, никогда в UserDefaults или SharedPreferences.
5. Лимит запросов на `/api/auth/*`. Turnstile на регистрации по почте.
6. Права доступа привязаны к `user.id`; покупки сохраняются, когда анонимный пользователь привязывает аккаунт.
7. Никакой своей криптографии и своих сессий. Никаких внешних сервисов входа (Clerk, Auth0, Firebase, Supabase).

## Ресурсы

- [Документация Better Auth](https://www.better-auth.com/docs) · [База (D1)](https://www.better-auth.com/docs/concepts/database) · [Apple](https://www.better-auth.com/docs/authentication/apple) · [Anonymous](https://www.better-auth.com/docs/plugins/anonymous) · [Bearer](https://www.better-auth.com/docs/plugins/bearer)
- [Sign in with Apple](https://developer.apple.com/sign-in-with-apple/) · [Android Credential Manager](https://developer.android.com/identity/sign-in/credential-manager-siwg)

---

Оригинал: [playbook/04-auth.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/04-auth.md) · MIT © Blake Anderson
