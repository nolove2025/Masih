import requests
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, ContextTypes, filters

# توکن تلگرام شما
TOKEN = "7643059241:AAGUY6hs_Cj1YD5euqxPWdBUTVSdvSvaZzI"
CMC_API_KEY = "ebf1df6c-511d-4daa-9a6a-6131c6abff67"

def get_price_cmc(symbol: str):
    url = "https://pro-api.coinmarketcap.com/v1/cryptocurrency/quotes/latest"
    headers = {
        "X-CMC_PRO_API_KEY": CMC_API_KEY,
        "Accepts": "application/json"
    }
    params = {"symbol": symbol.upper(), "convert": "USD"}

    try:
        response = requests.get(url, headers=headers, params=params)
        data = response.json()

        if symbol.upper() in data["data"]:
            price = data["data"][symbol.upper()]["quote"]["USD"]["price"]
            return {"symbol": symbol.upper(), "price": price}, None
        else:
            return None, f"⚠️ توکن '{symbol.upper()}' پیدا نشد یا نامعتبره."
    except Exception as e:
        return None, f"❌ خطا در اتصال به CoinMarketCap: {str(e)}"

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("سلام فرمانده، نماد توکنی که می‌خوای تحلیلش کنم رو بفرست.")

def simulate_quant_analysis(symbol: str, price: float):
    sentiment = ""
    if price > 10:
        sentiment = "بازار در حال ورود به **فاز انفجار ساختاری**ست. نشانه‌های پول‌هوشمند مشخصه."
    elif price > 1:
        sentiment = "الگوی فشار خریدار در حال تثبیت قیمت. **آغاز احتمالی روند صعودی میان‌مدت**."
    elif price > 0.1:
        sentiment = "وضعیت **نوسانی با پتانسیل پامپ سریع** دیده میشه. بازیگران بزرگ در کمین‌اند."
    else:
        sentiment = "ریسک بالا: **تله نهنگ‌ها و بازی لیکوییدیشن** در جریان است."

    rr = round((1.5 + price % 1) / (0.5 + (1 - price % 1)), 2)
    confidence = round(75 + price % 25, 1)
    entry = round(price * 0.98, 6)
    sl = round(price * 0.93, 6)
    tp = round(price * 1.15, 6)

    return (
        f"🧠 تحلیل کوانتومی برای {symbol.upper()}:\n\n"
        f"» قیمت فعلی: {price:.6f} $\n"
        f"{sentiment}\n\n"
        f"سیگنال احتمالی:\n"
        f"◾ ورود (Entry): {entry} $\n"
        f"◾ حد ضرر (SL): {sl} $\n"
        f"◾ تارگت سود (TP): {tp} $\n"
        f"◾ نسبت ریسک به ریوارد (RR): {rr}\n"
        f"◾ اعتماد AI: {confidence}%\n\n"
        f"**مدل تحلیلگری مسعود فعال شد. مراقب دام‌های روانی بازار باش.**"
    )

async def analyze_token(update: Update, context: ContextTypes.DEFAULT_TYPE):
    symbol = update.message.text.strip()
    await update.message.reply_text("در حال انتظار برای تحلیل با ذهن  مسعود از بازار...")

    data, err = get_price_cmc(symbol)
    if err:
        await update.message.reply_text(err)
        return

    result = simulate_quant_analysis(data["symbol"], data["price"])
    await update.message.reply_text(result, parse_mode="Markdown")

if __name__ == "__main__":
    app = ApplicationBuilder().token(TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, analyze_token))
    app.run_polling()



اینو چگونه در گیت هاپ ذخیره کنم
