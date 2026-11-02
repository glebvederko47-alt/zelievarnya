# Зельеварня

Головоломка-слияние для Telegram с покупкой кристаллов маны за Telegram Stars.

- `index.html` — сама игра (Telegram Mini App). Без сервера работает целиком, кроме покупок.
  Открыть с `?demo` — пробные покупки без оплаты.
- `server/worker.js` — сервер на Cloudflare Workers: счета в Stars, подтверждение оплаты,
  начисление кристаллов (ровно один раз на платёж), облачные сохранения, команды бота.
- `server/test_worker.mjs` — 19 проверок сервера без денег и без Cloudflare: `node server/test_worker.mjs`.

## Экономика

| Валюта | Откуда | На что |
|---|---|---|
| 🪙 Монеты | заказы, продажа предметов, лопнувшие пузыри | энергия +25, сундук алхимика |
| ⚡ Энергия | +1 каждые 90 с (до 100), уровни, подарок дня | нажатие на генератор |
| 💎 Кристаллы маны | **Telegram Stars**, уровни (+5), подарок дня (+5) | энергия +100, удача, ряд поля, ячейка склада, пузыри |

Пакеты: 80 💎 — 50 ⭐ · 260 💎 — 150 ⭐ · 900 💎 — 500 ⭐ · 2000 💎 — 1000 ⭐ · набор новичка (250 💎 + 200 ⚡) — 99 ⭐, один раз.
Цены меняются в двух местах: `PACKS`/`STARTER` в `index.html` и `PACKS` в `server/worker.js`.

## Запуск

1. **Бот.** В @BotFather: `/newbot` → получите токен → положите его в `server/token.txt`.
2. **Игра на GitHub Pages.** Репозиторий `zelievarnya`, файл `index.html` в корне, Pages из ветки `main`.
3. **Сервер.** Нужен бесплатный аккаунт Cloudflare. В папке `server`:
   ```
   npx wrangler login
   npx wrangler d1 create zelievarnya          # вставьте database_id в wrangler.toml
   npx wrangler d1 execute zelievarnya --remote --file=schema.sql
   npx wrangler secret put BOT_TOKEN           # токен бота
   npx wrangler secret put ADMIN_ID            # ваш Telegram id (для /refund)
   npx wrangler deploy                         # выдаст адрес https://zelievarnya.<имя>.workers.dev
   ```
   В `wrangler.toml` впишите свой ник в `SUPPORT_CONTACT`.
4. **Связать бота с сервером:** `python setup_bot.py https://zelievarnya.<имя>.workers.dev`,
   затем `npx wrangler secret put WEBHOOK_SECRET` со значением из `webhook_secret.txt`.
5. **Включить покупки в игре:** в `index.html` впишите адрес сервера в `const API = '...'`, а имя бота без @ — в `const BOT = '...'` (для ссылок-приглашений), и обновите GitHub Pages.

## Правила Telegram для платежей
- Цифровые товары продаются только за Stars (валюта `XTR`), провайдер оплаты не нужен.
- Команда `/paysupport` обязательна: на вопросы по покупкам отвечаете вы.
- Возврат: напишите боту `/refund <id игрока> <charge_id>` (только с аккаунта из `ADMIN_ID`).
- Кристаллы нельзя вывести или обменять обратно на Stars; шансы сундука показаны в игре.
