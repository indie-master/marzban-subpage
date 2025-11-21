# Шаблон страницы подписки для Marzban (SwiftlessVPN)

Кастомная страница подписки для панели [Marzban](https://github.com/Gozargah/Marzban):

- Одна точка входа для пользователя
- Универсальный клиент **Happ** для всех устройств
- Кнопка «Добавить подписку» (deeplink в Happ)
- Кнопка «Добавить роутинг» для Happ
- Выпадающий список всех конфигов из подписки (vless://, vmess://, trojan://, ss:// …)
- Ссылки на Telegram-канал и чат поддержки настраиваются через переменные в шаблоне
- Пошаговые инструкции с картинками для разных платформ

---

## Возможности

- Красивый и понятный интерфейс (светлая/тёмная тема)
- Happ как основной клиент:
  - iOS
  - Android
  - Windows
  - macOS
  - Linux
- Импорт подписки в Happ:
  - `happ://add/...` — подставляется текущая ссылка подписки
- Импорт роутинга:
  - `happ://routing/add/...` — зашит рекомендованный JSON для РФ
- Разбор подписки:
  - шаблон забирает содержимое по URL подписки
  - при необходимости декодирует base64
  - разбивает на строки и фильтрует только конфиги
  - выводит их в сворачиваемый список, по клику копирует конфиг в буфер
- Линки Telegram вынесены в переменные
- Инструкции со скриншотами (если залить картинки на сервер)

---

## Требования

- Установленный Marzban (Docker-версия или установка через `Marzban-scripts`)
- Включена подписка (`XRAY_SUBSCRIPTION_URL_PREFIX` настроен и панель доступна по HTTPS)

---

## Установка

1. **Скачать шаблон**

```bash
sudo mkdir -p /var/lib/marzban/templates/subscription

sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html
```

2. **Включить использование кастомных шаблонов в Marzban**

```bash
echo 'CUSTOM_TEMPLATES_DIRECTORY="/var/lib/marzban/templates/"' | sudo tee -a /opt/marzban/.env
echo 'SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"' | sudo tee -a /opt/marzban/.env
```

3. **Перезапустить Marzban**

```bash
sudo marzban restart
```

После этого все ссылки вида `/sbscr/...` будут открываться через этот шаблон.

---

## Настройка

### 1. Ссылки на Telegram-канал и чат поддержки

В начале файла `subscription/index.html` есть переменные:

```jinja2
{% set TELEGRAM_CHANNEL_URL = TELEGRAM_CHANNEL_URL | default('https://t.me/your_channel_here') %}
{% set TELEGRAM_SUPPORT_URL = TELEGRAM_SUPPORT_URL | default('https://t.me/your_support_chat_here') %}
```

Простой вариант: поменять значения по умолчанию прямо в шаблоне:

```jinja2
{% set TELEGRAM_CHANNEL_URL = 'https://t.me/swiftlessvpn' %}
{% set TELEGRAM_SUPPORT_URL = 'https://t.me/swiftlessvpn_support' %}
```

Эти переменные используются в блоке «Подписка» для ссылок:

* «Телеграм канал»
* «Чат поддержки»

Так в репозитории остаются безопасные заглушки, а реальные ссылки прописываются только на вашем сервере.

---

### 2. Скриншоты для инструкций Happ

В каждом блоке приложения (iOS / Android / Windows / macOS / Linux) есть такой фрагмент:

```html
<img src="/statics/subscription/happ/ios-step1.png"
     class="img-fluid rounded shadow-sm"
     alt="Happ iOS шаг 1"
     onerror="this.style.display='none';">
```

Чтобы добавить свои картинки:

1. Создайте директорию для статики:

```bash
sudo mkdir -p /opt/marzban/statics/subscription/happ
```

2. Скопируйте туда картинки, например:

```bash
sudo cp ios-step1.png /opt/marzban/statics/subscription/happ/
sudo cp ios-step2.png /opt/marzban/statics/subscription/happ/
sudo cp ios-step3.png /opt/marzban/statics/subscription/happ/
```

3. Убедитесь, что Marzban/реверс-прокси раздаёт `/statics` из `/opt/marzban/statics`
   (для стандартной Docker-установки это уже так).

Имя файла и путь можно изменить, главное — поправить `src` в `index.html` под свою структуру.

---

### 3. Список конфигов из подписки

В разделе «Подписка»:

* сам URL подписки **скрыт**, используется только внутри шаблона,
* пользователь видит:

  * кнопку «Скопировать подписку»
  * кнопку «Показать конфиги из подписки»

Кнопка «Показать конфиги из подписки»:

1. Делает запрос по URL подписки пользователя
2. При необходимости декодирует base64
3. Разбивает текст на строки
4. Оставляет только строки с `://`
5. Отображает их в свернутом списке; клик по строке копирует конфиг в буфер

Если конфигов нет или подписка недоступна, выводится аккуратное сообщение об ошибке.

---

### 4. Обновление шаблона

Чтобы обновиться до новой версии шаблона:

```bash
sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html

sudo marzban restart
```

Если вы меняли текст, ссылки или пути к картинкам, держите свои правки в отдельной ветке или просто аккуратно переносите их после обновления.

---

## Лицензия

MIT (см. исходный репозиторий).
