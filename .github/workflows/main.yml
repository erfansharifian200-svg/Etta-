import os
import time
import json
import requests

from eitaa.client import EitaaClient

EITAA_SESSION = os.getenv("EITAA_SESSION")
TELEGRAM_BOT_TOKEN = os.getenv("TELEGRAM_BOT_TOKEN")
TELEGRAM_CHAT_ID = os.getenv("TELEGRAM_CHAT_ID")

EITAA_CHANNEL = "irimedu"
STATE_FILE = "state.json"
CHECK_INTERVAL = 10


def load_state():
    try:
        with open(STATE_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except Exception:
        return {"last_id": 0}


def save_state(last_id):
    with open(STATE_FILE, "w", encoding="utf-8") as f:
        json.dump({"last_id": last_id}, f)


def send_telegram(text):
    url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"

    response = requests.post(
        url,
        json={
            "chat_id": TELEGRAM_CHAT_ID,
            "text": text,
            "disable_web_page_preview": False,
        },
        timeout=30,
    )

    response.raise_for_status()


def main():
    if not EITAA_SESSION:
        raise RuntimeError("EITAA_SESSION تنظیم نشده است.")

    if not TELEGRAM_BOT_TOKEN:
        raise RuntimeError("TELEGRAM_BOT_TOKEN تنظیم نشده است.")

    if not TELEGRAM_CHAT_ID:
        raise RuntimeError("TELEGRAM_CHAT_ID تنظیم نشده است.")

    client = EitaaClient(session=EITAA_SESSION)
    state = load_state()

    print(f"Watching Eitaa channel: @{EITAA_CHANNEL}")

    while True:
        try:
            messages = client.get_messages(EITAA_CHANNEL)

            messages = sorted(
                messages,
                key=lambda x: getattr(x, "id", 0)
            )

            for message in messages:
                message_id = getattr(message, "id", 0)

                if message_id <= state["last_id"]:
                    continue

                text = getattr(message, "text", None)

                if text:
                    send_telegram(text)
                    print(f"Forwarded Eitaa message {message_id}")

                state["last_id"] = message_id
                save_state(message_id)

        except Exception as e:
            print("ERROR:", e)

        time.sleep(CHECK_INTERVAL)


if __name__ == "__main__":
    main()
