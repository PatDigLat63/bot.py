# bot.py
import requests
from telegram import Update
from telegram.ext import Application, CommandHandler, ContextTypes

BOT_TOKEN = "8776258087:AAEmOeX0f8cUvD15d1yfQPrc4Ok2YxyfgL4"
HELIUS_KEY = "a5096800-5d41-4c8d-9f12-40c75a7392fa"
BIRDEYE_KEY = "fb26dbd60bb745cbaf20beaca37b6e42"
JUPITER_PRIVATE_KEY = "3D4uZu2k84Ch45dxQ5vXhWAZVSnk29D91a4n6gZ8Z6h6dQjcJWiC4zU53oAavKctrcagWCpJr3ctDQZrynGt8MMk"
GROUP_CHAT_ID = -5191032792

def get_price(symbol: str) -> str:
    url = "https://public-api.birdeye.so/public/price"
    headers = {"X-API-KEY": BIRDEYE_KEY}
    params = {"address": symbol}
    try:
        r = requests.get(url, headers=headers, params=params, timeout=10)
        if r.status_code == 200:
            data = r.json()
            price = data.get("data", {}).get("value")
            if price:
                return f"💰 {symbol.upper()} price: ${price:.6f}"
            else:
                return f"Price for {symbol} not found."
        else:
            return f"API error: {r.status_code}"
    except Exception as e:
        return f"Error: {e}"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Omega Dynasty Commander active.\nUse /price SYMBOL")

async def price(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if not context.args:
        await update.message.reply_text("Usage: /price SOL")
        return
    sym = context.args[0].upper()
    result = get_price(sym)
    await update.message.reply_text(result)

def main():
    app = Application.builder().token(BOT_TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("price", price))
    print("Bot running...")
    app.run_polling()

if __name__ == "__main__":
    main()
