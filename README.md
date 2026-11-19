import os
import asyncio
from aiogram import Bot, Dispatcher, types, F
from aiogram.filters import Command
from aiogram.types import ReplyKeyboardMarkup, KeyboardButton
from aiohttp import web

TOKEN = os.getenv("BOT_TOKEN")

bot = Bot(token=TOKEN)
dp = Dispatcher()

# Создаем постоянные кнопки внизу экрана: [ 🚀 Старт ] и [ 🎁 Халява ]
main_keyboard = ReplyKeyboardMarkup(
    keyboard=[
        [KeyboardButton(text="🚀 Старт"), KeyboardButton(text="🎁 Халява")]
    ],
    resize_keyboard=True
)

# Ответ на команду /start или нажатие кнопки "🚀 Старт"
@dp.message(Command("start"))
@dp.message(F.text == "🚀 Старт")
async def start_handler(message: types.Message):
    await message.answer(
        "👋 **Добро пожаловать в бота скидок и халявы!**\n\n"
        "Нажимай на кнопку **«🎁 Халява»** внизу экрана, чтобы получить актуальные промокоды и секретные скидки на маркетплейсах!",
        reply_markup=main_keyboard,
        parse_mode="Markdown"
    )

# Ответ на нажатие кнопки "🎁 Халява"
@dp.message(F.text == "🎁 Халява")
async def halyava_handler(message: types.Message):
    text = (
        "🔥 **СВЕЖАЯ ХАЛЯВА И ПРОМОКОДЫ** 🔥\n\n"
        "*(Нажми на промокод, чтобы быстро его скопировать)*\n\n"
        "🟣 **WILDBERRIES**\n"
        "• Товары со скидкой до 90% (ошибки цен):\n"
        "👉 [Смотреть подборку на WB](https://www.wildberries.ru)\n\n"
        "🔵 **OZON**\n"
        "• Скидка 500 ₽ на первый заказ от 2500 ₽\n"
        "Промокод: `OZON500SALE`\n"
        "👉 [Перейти на OZON](https://www.ozon.ru)\n\n"
        "🟡 **ЯНДЕКС МАРКЕТ**\n"
        "• Скидка 1000 ₽ на электронику и одежду\n"
        "Промокод: `MARKET1000`\n"
        "👉 [Перейти в Яндекс Маркет](https://market.yandex.ru)\n\n"
        "🟢 **МЕГАМАРКЕТ**\n"
        "• Скидка 1500 ₽ от 3000 ₽ на технику\n"
        "Промокод: `MEGA1500`\n"
        "👉 [Перейти в Мегамаркет](https://megamarket.ru)\n\n"
        "🍕 **КУПЕР (СБЕРМАРКЕТ)**\n"
        "• Продукты со скидкой 600 ₽ от 2000 ₽\n"
        "Промокод: `KUPER600`"
    )
    await message.answer(
        text, 
        reply_markup=main_keyboard, 
        parse_mode="Markdown", 
        disable_web_page_preview=True
    )

# Микро-сервер для бесплатного тарифа Render (чтобы бот не отключался)
async def run_dummy_server():
    app = web.Application()
    app.router.add_get('/', lambda r: web.Response(text="Bot is alive!"))
    runner = web.AppRunner(app)
    await runner.setup()
    port = int(os.getenv("PORT", 8080))
    site = web.TCPSite(runner, "0.0.0.0", port)
    await site.start()

async def main():
    await run_dummy_server()
    print("Бот успешно запущен!")
    await dp.start_polling(bot)

if __name__ == "__main__":
    asyncio.run(main())
