# Marzban Subscription Page (indie-master Edition)

Custom subscription page template for [Marzban](https://github.com/Gozargah/Marzban), focused on:

- Clean layout with dark/light mode
- Single universal client: **Happ**
- One-click subscription import into Happ
- Routing import button for Happ
- Expandable list of raw config links (vless://, vmess://, ss://, trojan://, …)
- Telegram channel & support chat links configurable via template variables
- Platform-specific step-by-step instructions with screenshots

> Russian documentation is available in [`README.ru.md`](./README.ru.md).

---

## Features

- ✅ Compatible with Marzban’s `SUBSCRIPTION_PAGE_TEMPLATE`
- ✅ Happ as the only recommended client (iOS, Android, Windows, macOS, Linux)
- ✅ Deep links:
  - `happ://add/...` to import the subscription
  - `happ://routing/add/...` to import routing rules
- ✅ Automatic parsing of the user’s subscription:
  - fetches subscription URL
  - decodes Base64 if needed
  - splits into individual links
  - shows them in a collapsible list with copy-on-click
- ✅ Dark mode toggle
- ✅ Easy to customize

---

## Installation

1. **Download template to Marzban templates directory**

```bash
sudo mkdir -p /var/lib/marzban/templates/subscription

sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html
```

2. **Tell Marzban to use custom templates**

Append to `/opt/marzban/.env`:

```bash
echo 'CUSTOM_TEMPLATES_DIRECTORY="/var/lib/marzban/templates/"' | sudo tee -a /opt/marzban/.env
echo 'SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"' | sudo tee -a /opt/marzban/.env
```

3. **Restart Marzban**

```bash
sudo marzban restart
```

After that, opening any user subscription link (`/sbscr/...`) will use this template.

---

## Configuration

### 1. Telegram channel & support chat links

At the top of `subscription/index.html` there are template variables:

```jinja2
{% set TELEGRAM_CHANNEL_URL = TELEGRAM_CHANNEL_URL | default('https://t.me/your_channel_here') %}
{% set TELEGRAM_SUPPORT_URL = TELEGRAM_SUPPORT_URL | default('https://t.me/your_support_chat_here') %}
```

You can configure them in two ways:

1. **Simple way (recommended):**
   Edit the default values directly in `index.html`:

   ```jinja2
   {% set TELEGRAM_CHANNEL_URL = 'https://t.me/your_channel_here' %}
   {% set TELEGRAM_SUPPORT_URL = 'https://t.me/your_support_chat_here' %}
   ```

2. **Advanced way:**
   Pass `TELEGRAM_CHANNEL_URL` and `TELEGRAM_SUPPORT_URL` from your Marzban backend
   when rendering the template (requires backend changes, not included here).

These variables are used in the “Подписка” card for:

* «Телеграм канал»
* «Чат поддержки»

---

### 2. Happ download & deeplinks

All Happ links and deeplinks (App Store, TestFlight, Google Play, APK, Windows, macOS, Linux, routing) are centralized in a small JS configuration object inside `index.html`.
If the official Happ download URLs change, update them there, redeploy the template and restart Marzban.

---

### 3. Screenshot images for instructions

Each platform section (iOS / Android / Windows / macOS / Linux) contains a block like:

```html
<img src="/statics/subscription/happ/ios-step1.png"
     class="img-fluid rounded shadow-sm"
     alt="Happ iOS step 1"
     onerror="this.style.display='none';">
```

To add your own screenshots:

1. Create a directory on the server:

   ```bash
   sudo mkdir -p /opt/marzban/statics/subscription/happ
   ```

2. Copy your PNG/JPEG files there, for example:

   * `/opt/marzban/statics/subscription/happ/ios-step1.png`
   * `/opt/marzban/statics/subscription/happ/ios-step2.png`
   * `/opt/marzban/statics/subscription/happ/ios-step3.png`

3. Make sure your Marzban container (or reverse proxy) serves `/statics` from `/opt/marzban/statics`.
   This is the default for the official Docker setup.

You can change filenames or paths in `index.html` if needed.

---

## Updating

To update the template from GitHub:

```bash
sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html

sudo marzban restart
```

If you have local customizations (texts, links, screenshots), you may want to keep them in a separate branch or re-apply after updating.

---

## License

MIT, see the original repository.
