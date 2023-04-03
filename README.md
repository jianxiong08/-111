# -111import requests
import json
import time
import telegram

# 欧易C2C API
url = 'https://c2c-api.okex.com/v2/c2c/trading/currencies'
headers = {
    'Content-type': 'application/json'
}

# Telegram Bot API
bot = telegram.Bot(token='5879026357:AAH_UtgaocwRaYI29--6UnE4nbTt5eCsVM8')

def get_usdt_cny_price():
    # 获取欧易C2C交易所USDT/CNY汇率
    response = requests.get(url, headers=headers)
    response_json = json.loads(response.content.decode('utf-8'))
    usdt_cny_price = ''
    for currency in response_json['data']:
        if currency['currency'] == 'usdt':
            usdt_cny_price = currency['price']
            break
    return usdt_cny_price

def send_price(update, context):
    # 发送最新报价
    chat_id = update.effective_chat.id
    price = get_usdt_cny_price()
    message = f"当前USDT/CNY汇率为{price}元"
    bot.send_message(chat_id=chat_id, text=message)

def main():
    # 添加机器人指令处理函数
    updater = telegram.ext.Updater(token='5879026357:AAH_UtgaocwRaYI29--6UnE4nbTt5eCsVM8', use_context=True)
    dispatcher = updater.dispatcher
    dispatcher.add_handler(telegram.ext.CommandHandler('price', send_price))

    # 开始运行机器人
    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
