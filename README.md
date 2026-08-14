# TelegramVPN

Професійний VPN-сервіс з інтеграцією Telegram, системою підписок та оплатою через Telegram Stars/Payments.

## Можливості

### VPN
- WireGuard та OpenVPN (конфігурація через API)
- Автоматичний вибір найшвидшого сервера
- Kill Switch та захист від витоку DNS
- Автоматичне перепідключення
- Статистика трафіку, часу роботи та пінгу

### Telegram
- Авторизація через Telegram Login Widget
- Особистий кабінет через Telegram Bot
- Оплата через Telegram Stars та Telegram Payments
- Генерація WireGuard конфігурацій
- Сповіщення про закінчення підписки
- Реферальна система

### Підписки
- 1 / 3 / 6 / 12 місяців
- Промокоди та автопродовження
- Автоматична активація після оплати

### Адмін-панель
- Керування користувачами та серверами
- Фінансова аналітика
- Журнал дій
- Система підтримки

## Архітектура

```
telegram-vpn/
├── backend/          # FastAPI REST API
├── bot/              # Telegram Bot (aiogram 3)
├── admin-panel/      # React адмін-панель
├── clients/          # Linux, Windows, Android клієнти
├── nginx/            # Reverse proxy + TLS
├── scripts/          # WireGuard setup, backup
└── docker-compose.yml
```

## Швидкий старт

### 1. Клонування та налаштування

```bash
cd telegram-vpn
cp .env.example .env
# Відредагуйте .env — додайте токени Telegram, паролі, домен
```

### 2. Налаштування Telegram Bot

1. Створіть бота через [@BotFather](https://t.me/BotFather)
2. Отримайте `TELEGRAM_BOT_TOKEN`
3. Увімкніть Payments: `/mybots` → Bot Settings → Payments
4. Для Stars: `/mybots` → Bot Settings → Stars
5. Додайте Login Widget домен: `/setdomain`

### 3. WireGuard на VPS

```bash
sudo bash scripts/setup-wireguard.sh
# Скопіюйте ключі в .env
```

### 4. SSL сертифікати

```bash
mkdir -p nginx/ssl
# Certbot або власні сертифікати в nginx/ssl/
```

### 5. Запуск

**На Linux з Podman (без Docker):**

```bash
bash scripts/start.sh
```

Скрипт автоматично налаштує `DOCKER_HOST` для rootless Podman.

**Або вручну:**

```bash
export DOCKER_HOST=unix://${XDG_RUNTIME_DIR}/podman/podman.sock
podman system service --time=0 &   # якщо сокет ще не запущений
podman compose -f docker-compose.dev.yml up -d --build
```

**З Docker:**

```bash
docker compose -f docker-compose.dev.yml up -d --build
```

**Production (з Nginx + SSL):**

```bash
docker compose up -d --build   # потребує SSL-сертифікати в nginx/ssl/
```

Сервіси (dev):
- API: `http://localhost:8000`
- Адмін-панель: `http://localhost:3000`
- Telegram Bot: polling mode (або webhook через nginx)

Production:
- API: `https://api.yourdomain.com`
- Адмін-панель: `https://panel.yourdomain.com`

### 6. Перший вхід в адмін-панель

Email та пароль з `.env`:
- `ADMIN_EMAIL`
- `ADMIN_PASSWORD`

## API Endpoints

| Method | Endpoint | Опис |
|--------|----------|------|
| POST | `/api/v1/auth/telegram` | Авторизація через Telegram Widget |
| POST | `/api/v1/auth/login` | Email + пароль (+ 2FA) |
| GET | `/api/v1/vpn/servers` | Список серверів |
| GET | `/api/v1/vpn/servers/fastest` | Найшвидший сервер |
| POST | `/api/v1/vpn/devices` | Створити пристрій |
| GET | `/api/v1/vpn/devices/{id}/config` | WireGuard конфіг |
| GET | `/api/v1/payments/subscription` | Поточна підписка |
| POST | `/api/v1/payments/purchase` | Створити платіж |
| GET | `/api/v1/admin/stats` | Статистика (admin) |

Повна документація: `https://api.yourdomain.com/docs` (якщо DEBUG=true)

## Клієнти

### Linux
```bash
cd clients/linux
pip install -r requirements.txt
python telegram_vpn.py
```

### Windows
```bash
cd clients/windows
pip install httpx
python telegram_vpn.py
```
Потрібен [WireGuard for Windows](https://www.wireguard.com/install/).

### Android
Див. `clients/android/README.md`

## Тарифи (за замовчуванням)

| План | Ціна (UAH) | Stars |
|------|-----------|-------|
| 1 місяць | 99 ₴ | 50 ⭐ |
| 3 місяці | 249 ₴ | 120 ⭐ |
| 6 місяців | 449 ₴ | 220 ⭐ |
| 12 місяців | 799 ₴ | 400 ⭐ |

## Безпека

- Argon2 для хешування паролів
- JWT авторизація (access + refresh tokens)
- TLS 1.3 через Nginx
- Rate limiting (SlowAPI)
- 2FA (TOTP)
- Автоматичне резервне копіювання PostgreSQL
- Kill Switch на клієнтах

## Telegram Bot команди

Бот працює через Reply Keyboard:
- 🔐 Моя підписка
- 💳 Купити VPN
- 📱 Пристрої
- ⚙️ Конфігурація
- 🔗 Реферали
- 🆘 Підтримка

## Оновлення

```bash
docker compose pull
docker compose up -d --build
```

---

# TelegramVPN (English)

Professional VPN service with Telegram integration, subscription system, and Telegram Stars/Payments.

## Quick Start

```bash
cp .env.example .env
# Edit .env with your tokens and domain
sudo bash scripts/setup-wireguard.sh
docker compose up -d --build
```

## Features

- WireGuard VPN with auto server selection
- Telegram Bot for account management and payments
- Telegram Stars & Telegram Payments support
- Admin panel with dark Telegram-style UI
- Referral system & promo codes
- Kill Switch, DNS leak protection, auto-reconnect
- Clients for Linux, Windows, Android
- Ukrainian & English localization

## License

MIT
