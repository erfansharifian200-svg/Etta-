# Eitaa → Telegram Forwarder

Forwards new text messages from the public Eitaa channel:

https://eitaa.com/irimedu

to a Telegram chat using a Telegram Bot.

## Environment variables

Create a `.env` file:

EITAA_SESSION=...
TELEGRAM_BOT_TOKEN=...
TELEGRAM_CHAT_ID=...

Never commit these credentials to GitHub.

## Install

pip install -r requirements.txt

## Run

python bot.py
