# trytelebot-
guyz i just try make bot on telegram but script doesn't work can u watch this ?
my bot should show gwei on the ethereum network automatically. 


import telebot
import requests
import schedule
import time

# Токен  бота
TOKEN = 'TELEGRAM_BOT_TOKEN'

# Ваш API ключ для доступа к Etherscan
ETHERSCAN_API_KEY = 'ETHERSCAN_API_KEY'

bot = telebot.TeleBot(TOKEN)

# Функция для получения цены газа
def get_gas_price(chat_id):
    try:
        # Запрос к Etherscan API для получения текущей средней цены газа
        response = requests.get(f'https://api.etherscan.io/api?module=gastracker&action=gasoracle&apikey={ETHERSCAN_API_KEY}')
        data = response.json()
        
        if data['status'] == '1':
            gas_price = data['result']['SafeGasPrice']
            bot.send_message(chat_id, f'Текущая цена газа в сети Ethereum: {gas_price} gwei')
        else:
            bot.send_message(chat_id, 'Не удалось получить цену газа.')
    except Exception as e:
        bot.send_message(chat_id, f'Произошла ошибка при получении цены газа: {str(e)}')

# Функция для отправки сообщения о старте
@bot.message_handler(commands=['start'])
def start(message):
    bot.send_message(message.chat.id, 'Привет! Я бот, который отслеживает цену газа в сети Ethereum.')
    schedule.every(1).minutes.do(lambda: get_gas_price(message.chat.id))

# Функция для отправки текущей цены газа по команде /gasprice
@bot.message_handler(commands=['gasprice'])
def gas_price_command_handler(message):
    get_gas_price(message.chat.id)

# Основная функция
if __name__ == "__main__":
    bot.polling(none_stop=True)
    while True:
        schedule.run_pending()
        time.sleep(1)
