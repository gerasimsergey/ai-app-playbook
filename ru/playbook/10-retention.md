# 10 · Удержание

> **По умолчанию:** годовые подписчики продлевают, только если приложение стало привычкой.
> **Агенту: читать, когда** делаешь уведомления, серии, виджеты, письма или возврат ушедших.

**Петля привычки:** триггер (пуш в привычное время, виджет, серия под угрозой) → одно маленькое действие → награда (прогресс, вывод) → вложение (история, серии, план).

| Инструмент | Как |
|---|---|
| **Пуши** | APNs и FCM из Worker. Спрашивай разрешение **после первого успеха**, никогда при запуске |
| **Умное время** | Cron Triggers + расписание каждого пользователя в D1, в его привычный час |
| **Серии** | С «заморозкой» серии |
| **Виджеты / Live Activities** | Прогресс за день на домашнем экране |
| **Письма по жизненному циклу** | Cloudflare Email Service через Queues: приветствие, совет на 3-й день, **конец пробного периода**, возврат |
| **Просьба об отзыве** | `SKStoreReviewController` / Play In-App Review после успеха |
| **Возврат ушедших** | Кампании Superwall и win-back предложения App Store |
| **Итоги недели** | «На этой неделе ты…», заодно карточка для шаринга |

**Правила:** каждый пуш полезен · учитывай часовые пояса и тихие часы · частоту выбирает пользователь · измеряй согласие на пуши и переходы из пуша, тестируй тексты · **всегда отправляй напоминание о конце пробного периода**.

## Ресурсы

- [User Notifications (APNs)](https://developer.apple.com/documentation/usernotifications) · [WidgetKit](https://developer.apple.com/documentation/widgetkit) · [ActivityKit](https://developer.apple.com/documentation/activitykit)
- [Firebase Cloud Messaging HTTP v1](https://firebase.google.com/docs/cloud-messaging/send/v1-api) (только FCM, больше ничего из Firebase)

---

Оригинал: [playbook/10-retention.md](https://github.com/blakeandersonw/ai-app-playbook/blob/main/playbook/10-retention.md) · MIT © Blake Anderson
