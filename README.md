# Шаблон страницы подписки Marzban

Готовый репозиторий с одним продуктом: кастомным шаблоном страницы подписки для Marzban.

Шаблон показывает карточку подписки, блок приложений, раздел помощи, тёмную тему, список конфигов, deeplink-кнопки для добавления подписки и роутинга в Happ, а также поддерживает кастомный заголовок и логотипы. Для быстрого просмотра без Marzban в репозитории есть статическое demo в `docs/`.

> English version: [`README.en.md`](./README.en.md)

## Возможности шаблона

- карточка подписки с именем, статусом, трафиком и сроком действия;
- ссылки на Telegram-канал и чат поддержки;
- кнопка копирования ссылки подписки;
- кнопка показа конфигов из подписки;
- список конфигов с копированием по клику;
- тёмная тема;
- deeplink-кнопка добавления подписки в Happ;
- deeplink-кнопка добавления роутинга в Happ;
- блок приложений для iOS, Android, Windows, macOS и Linux;
- раздел помощи;
- кастомный заголовок сервиса;
- кастомные логотипы сервиса и Happ;
- статическая demo-страница в `docs/`, визуально повторяющая основной шаблон.

## Структура репозитория

```text
.
├── README.md
├── README.en.md
├── docs/
│   └── index.html
└── subscription/
    └── index.html
```

- `subscription/index.html` — основной Jinja-шаблон для Marzban.
- `docs/index.html` — статическая demo-страница на безопасных demo-данных, без Jinja и без backend-зависимостей.

## Установка

1. Создайте директорию для шаблона:

```bash
sudo mkdir -p /var/lib/marzban/templates/subscription
```

2. Скачайте актуальный шаблон:

```bash
sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html
```

3. Укажите Marzban использовать кастомный шаблон:

```bash
echo 'CUSTOM_TEMPLATES_DIRECTORY="/var/lib/marzban/templates/"' | sudo tee -a /opt/marzban/.env
echo 'SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"' | sudo tee -a /opt/marzban/.env
```

4. Перезапустите Marzban:

```bash
sudo marzban restart
```

После этого пользовательские ссылки вида `/sbscr/...` будут открываться через этот шаблон.

## Подключение шаблона в Marzban

Для работы нужен активный кастомный шаблон через `CUSTOM_TEMPLATES_DIRECTORY` и `SUBSCRIPTION_PAGE_TEMPLATE`. Сам шаблон рассчитан на стандартную Jinja-рендеринг-логику страницы подписки Marzban и использует реальные данные пользователя: имя, статус, трафик, дату истечения и URL подписки.

## Настройка кастомных параметров

В начале `subscription/index.html` используются следующие переменные:

### `SERVICE_TITLE`
- Что делает: задаёт заголовок страницы в верхнем блоке.
- Fallback: `Подписка`.
- Пример:

```jinja2
{% set SERVICE_TITLE = 'My VPN' %}
```

### `SERVICE_LOGO_URL`
- Что делает: задаёт URL логотипа сервиса.
- Fallback: пустая строка.
- Используется только если `SERVICE_LOGO_PATH` пустой.
- Пример:

```jinja2
{% set SERVICE_LOGO_URL = 'https://example.com/logo.png' %}
```

### `SERVICE_LOGO_PATH`
- Что делает: задаёт web-accessible path для логотипа сервиса.
- Fallback: пустая строка.
- Имеет приоритет над `SERVICE_LOGO_URL`.
- Пример:

```jinja2
{% set SERVICE_LOGO_PATH = '/statics/subscription/branding/logo.png' %}
```

### `HAPP_LOGO_URL`
- Что делает: задаёт URL логотипа Happ.
- Fallback: встроенное base64-изображение.
- Используется только если `HAPP_LOGO_PATH` пустой.
- Пример:

```jinja2
{% set HAPP_LOGO_URL = 'https://example.com/happ-logo.png' %}
```

### `HAPP_LOGO_PATH`
- Что делает: задаёт web-accessible path для логотипа Happ.
- Fallback: пустая строка.
- Имеет приоритет над `HAPP_LOGO_URL`.
- Пример:

```jinja2
{% set HAPP_LOGO_PATH = '/statics/subscription/branding/happ-logo.png' %}
```

### `TELEGRAM_CHANNEL_URL`
- Что делает: задаёт ссылку на Telegram-канал в карточке подписки.
- Fallback: `https://t.me/your_channel_here`.
- Пример:

```jinja2
{% set TELEGRAM_CHANNEL_URL = 'https://t.me/my_channel' %}
```

### `TELEGRAM_SUPPORT_URL`
- Что делает: задаёт ссылку на чат поддержки в карточке подписки.
- Fallback: `https://t.me/your_support_chat_here`.
- Пример:

```jinja2
{% set TELEGRAM_SUPPORT_URL = 'https://t.me/my_support' %}
```

## Настройка логотипов

Логика выбора логотипов в шаблоне одинакова для сервиса и Happ:

1. если задан `*_PATH`, используется он;
2. иначе, если задан `*_URL`, используется он;
3. иначе логотип не показывается.

Примеры итоговой логики:

```jinja2
{% set SERVICE_LOGO_SRC = SERVICE_LOGO_PATH if SERVICE_LOGO_PATH else SERVICE_LOGO_URL %}
{% set HAPP_LOGO_SRC = HAPP_LOGO_PATH if HAPP_LOGO_PATH else HAPP_LOGO_URL %}
```

Важно: `*_PATH` должен быть именно web-accessible path, а не путь файловой системы.

Правильно:

```text
/statics/subscription/branding/logo.png
/statics/subscription/branding/happ-logo.png
```

Неправильно:

```text
/var/lib/marzban/templates/logo.png
/opt/marzban/statics/logo.png
```

## Настройка Telegram-ссылок

Чтобы задать свои ссылки, достаточно поменять значения переменных в начале `subscription/index.html` или передавать их из backend при рендеринге шаблона.

Базовый вариант — прописать свои значения прямо в файле:

```jinja2
{% set TELEGRAM_CHANNEL_URL = 'https://t.me/my_channel' %}
{% set TELEGRAM_SUPPORT_URL = 'https://t.me/my_support' %}
```

## Настройка Happ

Шаблон уже содержит:

- ссылки на Happ для iOS, Android, Windows, macOS и Linux;
- deeplink добавления подписки через `happ://add/...`;
- deeplink добавления роутинга через `happ://routing/add/...`;
- блоки инструкций для каждой платформы.

Если официальные ссылки Happ изменятся, обновите их в `subscription/index.html` и затем перезагрузите шаблон на сервере.

## Demo

Статическое demo находится в `docs/index.html`.

Особенности demo:

- без Jinja;
- без зависимости от backend Marzban;
- со статическими безопасными данными;
- визуально повторяет текущий основной шаблон из `subscription/index.html`.

Используемые demo-данные:

- имя подписки: `demo_user`;
- статус: `Активна`;
- трафик: `12.4 GB / 100 GB`;
- истекает: `2027-04-18 09:05:36`;
- Telegram-ссылки: безопасные заглушки;
- конфиги: демонстрационные VLESS / Shadowsocks / Trojan ссылки.

GitHub Pages URL для этого репозитория:

```text
https://indie-master.github.io/marzban-subpage/
```

Если GitHub Pages ещё не включён, его нужно настроить на публикацию из ветки `master` и директории `docs/`.

## Обновление

Чтобы обновить шаблон на сервере:

```bash
sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html
sudo marzban restart
```

Если вы обновляли документацию или demo, для Marzban-сервера нужно переобновлять только `subscription/index.html`.

## Лицензия

В репозитории не указан отдельный файл лицензии. Используйте шаблон в соответствии с политикой вашего проекта и правилами исходного репозитория.
