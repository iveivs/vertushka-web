# API

Бэкенд — [vertushka-api](https://github.com/iveivs/vertushka-api), запуск описан в его README.

**Источник правды — Swagger:** `http://localhost:4000/docs`. Там точные схемы запросов и ответов, и любую ручку можно вызвать из браузера. Здесь — короткий справочник, чтобы быстро найти нужную ручку.

Демо-аккаунты: `vinyl_max`, `jazz_cat`, `crate_digger`, `soul_sister`, `bass_head`, пароль у всех `password123`.

## Договорённости

- Все ручки под `/api`, JSON в обе стороны, поля в `camelCase`.
- Авторизация: заголовок `Authorization: Bearer <accessToken>`. Токен живёт 7 дней.
- Списки отдаются страницами: `{ items, total, page, limit, totalPages }`.
- Ошибки всегда одинаковые: `{ "error": { "code", "message", "details?" } }`. Для `VALIDATION_ERROR` в `details` лежат ошибки по полям: `{ "password": "Минимум 8 символов" }`.
- `coverUrl` и `avatarUrl` — относительные пути (`/uploads/…`), фронт склеивает их с адресом API. `null` — картинки нет, показываем заглушку.
- Лайк и снятие лайка идемпотентны: повторный запрос — не ошибка.

## Ручки

🔒 — нужен токен. 👤 — токен необязателен, но с ним заполняется `likedByMe`.

### Авторизация

| Метод | Путь | Что делает |
| --- | --- | --- |
| `POST` | `/api/auth/register` | Регистрация → `{ accessToken, user }` |
| `POST` | `/api/auth/login` | Вход по email или username → `{ accessToken, user }` |
| `GET` 🔒 | `/api/auth/me` | Текущий пользователь |

### Пластинки

| Метод | Путь | Что делает |
| --- | --- | --- |
| `GET` 👤 | `/api/records` | Лента. Параметры: `search`, `genre` (через запятую), `yearFrom`, `yearTo`, `sort`, `page`, `limit` |
| `GET` 👤 | `/api/records/:id` | Одна пластинка |
| `POST` 🔒 | `/api/records` | Добавить |
| `PATCH` 🔒 | `/api/records/:id` | Изменить (только владелец) |
| `DELETE` 🔒 | `/api/records/:id` | Удалить (только владелец) |
| `POST` 🔒 | `/api/records/:id/cover` | Обложка: `multipart/form-data`, поле `cover`, JPEG/PNG/WebP до 5 МБ |

Сортировки `sort`: `new` (по умолчанию), `old`, `popular`, `year_asc`, `year_desc`, `artist`.

### Лайки и комментарии

| Метод | Путь | Что делает |
| --- | --- | --- |
| `POST` 🔒 | `/api/records/:id/like` | Поставить лайк → `{ likesCount, likedByMe }` |
| `DELETE` 🔒 | `/api/records/:id/like` | Убрать лайк → `{ likesCount, likedByMe }` |
| `GET` | `/api/records/:id/comments` | Комментарии, новые сверху, по 10 на страницу |
| `POST` 🔒 | `/api/records/:id/comments` | Написать комментарий |
| `DELETE` 🔒 | `/api/comments/:id` | Удалить свой комментарий |

### Пользователи

| Метод | Путь | Что делает |
| --- | --- | --- |
| `GET` | `/api/users/:username` | Профиль + `recordsCount`, `likesReceived` |
| `GET` 👤 | `/api/users/:username/records` | Фонотека пользователя (фильтры как у ленты) |
| `GET` 👤 | `/api/users/:username/likes` | Что пользователь лайкнул |
| `PATCH` 🔒 | `/api/users/me` | Изменить `bio` |
| `POST` 🔒 | `/api/users/me/avatar` | Аватар: `multipart/form-data`, поле `avatar` |

### Справочники

| Метод | Путь | Что делает |
| --- | --- | --- |
| `GET` | `/api/genres` | Список жанров |
| `GET` | `/api/conditions` | Градации состояния винила с расшифровкой |

## Режим «плохой сети»

Бэкенд умеет притворяться медленным и нестабильным. Это нужно, когда делаешь лоадеры и обработку ошибок:

```bash
DELAY_MS=1500 FAIL_RATE=0.2 npm run dev   # каждый ответ через 1.5 с, 20% запросов падают с 500
```

## Нужна новая ручка или нашёл баг

Заведи issue в [vertushka-api](https://github.com/iveivs/vertushka-api/issues) с меткой `backend`. Не обходи проблему на фронте.
