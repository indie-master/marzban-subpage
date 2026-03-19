# Marzban Subscription Page Template

A repository focused on one product only: a custom subscription page template for Marzban.

The template includes a subscription card, apps section, help section, dark mode, config list, Happ deeplink buttons for subscription and routing import, and support for a custom title and logos. A static demo is included in `docs/` for previewing the page without Marzban.

> Russian main documentation: [`README.md`](./README.md)

## Features

- subscription card with name, status, traffic, and expiration date;
- Telegram channel and support chat links;
- copy subscription link button;
- button to show configs from the subscription;
- config list with click-to-copy behavior;
- dark mode;
- Happ deeplink button for adding the subscription;
- Happ deeplink button for adding routing;
- apps section for iOS, Android, Windows, macOS, and Linux;
- help section;
- custom service title;
- custom service and Happ logos;
- static demo page in `docs/` that visually mirrors the main template.

## Repository Structure

```text
.
├── README.md
├── README.en.md
├── docs/
│   └── index.html
└── subscription/
    └── index.html
```

- `subscription/index.html` — main Jinja template for Marzban.
- `docs/index.html` — static demo page with safe demo data, no Jinja, and no backend dependency.

## Installation

1. Create the template directory:

```bash
sudo mkdir -p /var/lib/marzban/templates/subscription
```

2. Download the current template:

```bash
sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html
```

3. Tell Marzban to use the custom template:

```bash
echo 'CUSTOM_TEMPLATES_DIRECTORY="/var/lib/marzban/templates/"' | sudo tee -a /opt/marzban/.env
echo 'SUBSCRIPTION_PAGE_TEMPLATE="subscription/index.html"' | sudo tee -a /opt/marzban/.env
```

4. Restart Marzban:

```bash
sudo marzban restart
```

After that, user subscription links such as `/sbscr/...` will open with this template.

## Connecting the Template in Marzban

The template expects the standard Marzban Jinja subscription-page context and uses real user data such as username, status, traffic, expiration date, and subscription URL.

## Custom Parameters

The following variables are used at the top of `subscription/index.html`.

### `SERVICE_TITLE`
- Purpose: sets the page title in the top header.
- Fallback: `Подписка`.
- Example:

```jinja2
{% set SERVICE_TITLE = 'My VPN' %}
```

### `SERVICE_LOGO_URL`
- Purpose: sets the service logo URL.
- Fallback: empty string.
- Used only if `SERVICE_LOGO_PATH` is empty.
- Example:

```jinja2
{% set SERVICE_LOGO_URL = 'https://example.com/logo.png' %}
```

### `SERVICE_LOGO_PATH`
- Purpose: sets the web-accessible path for the service logo.
- Fallback: empty string.
- Takes priority over `SERVICE_LOGO_URL`.
- Example:

```jinja2
{% set SERVICE_LOGO_PATH = '/statics/subscription/branding/logo.png' %}
```

### `HAPP_LOGO_URL`
- Purpose: sets the Happ logo URL.
- Fallback: embedded base64 image.
- Used only if `HAPP_LOGO_PATH` is empty.
- Example:

```jinja2
{% set HAPP_LOGO_URL = 'https://example.com/happ-logo.png' %}
```

### `HAPP_LOGO_PATH`
- Purpose: sets the web-accessible path for the Happ logo.
- Fallback: empty string.
- Takes priority over `HAPP_LOGO_URL`.
- Example:

```jinja2
{% set HAPP_LOGO_PATH = '/statics/subscription/branding/happ-logo.png' %}
```

### `TELEGRAM_CHANNEL_URL`
- Purpose: sets the Telegram channel link in the subscription card.
- Fallback: `https://t.me/your_channel_here`.
- Example:

```jinja2
{% set TELEGRAM_CHANNEL_URL = 'https://t.me/my_channel' %}
```

### `TELEGRAM_SUPPORT_URL`
- Purpose: sets the Telegram support chat link in the subscription card.
- Fallback: `https://t.me/your_support_chat_here`.
- Example:

```jinja2
{% set TELEGRAM_SUPPORT_URL = 'https://t.me/my_support' %}
```

## Logo Logic

The logo priority is the same for the service logo and the Happ logo:

1. use `*_PATH` if set;
2. otherwise use `*_URL` if set;
3. otherwise do not render the logo.

Example logic:

```jinja2
{% set SERVICE_LOGO_SRC = SERVICE_LOGO_PATH if SERVICE_LOGO_PATH else SERVICE_LOGO_URL %}
{% set HAPP_LOGO_SRC = HAPP_LOGO_PATH if HAPP_LOGO_PATH else HAPP_LOGO_URL %}
```

Important: `*_PATH` must be a web-accessible path, not a filesystem path.

Correct:

```text
/statics/subscription/branding/logo.png
/statics/subscription/branding/happ-logo.png
```

Incorrect:

```text
/var/lib/marzban/templates/logo.png
/opt/marzban/statics/logo.png
```

## Telegram Links

You can set your own Telegram links by editing the variables in `subscription/index.html` or by passing them from the backend during template rendering.

Simple file-based example:

```jinja2
{% set TELEGRAM_CHANNEL_URL = 'https://t.me/my_channel' %}
{% set TELEGRAM_SUPPORT_URL = 'https://t.me/my_support' %}
```

## Happ Setup

The template already includes:

- Happ download links for iOS, Android, Windows, macOS, and Linux;
- subscription deeplink via `happ://add/...`;
- routing deeplink via `happ://routing/add/...`;
- instruction blocks for each platform.

If Happ official URLs change, update them in `subscription/index.html` and redeploy the template.

## Demo

The static demo is located at `docs/index.html`.

Demo properties:

- no Jinja;
- no Marzban backend dependency;
- safe static demo data;
- visually mirrors the current main template from `subscription/index.html`.

Demo values include:

- subscription name: `demo_user`;
- status: `Активна`;
- traffic: `12.4 GB / 100 GB`;
- expiration: `2027-04-18 09:05:36`;
- Telegram links: safe placeholders;
- configs: demo VLESS / Shadowsocks / Trojan links.

GitHub Pages URL for this repository:

https://indie-master.github.io/marzban-subpage/

If GitHub Pages is not enabled yet, publish the `docs/` directory from the `master` branch.

## Updating

To update the template on the server:

```bash
sudo wget -O /var/lib/marzban/templates/subscription/index.html \
  https://raw.githubusercontent.com/indie-master/marzban-subpage/master/subscription/index.html
sudo marzban restart
```

If you changed only the documentation or demo, only `subscription/index.html` needs to be redeployed to the Marzban server.

## License

There is no separate license file in the repository. Use the template according to your project policy and the rules of the source repository.
