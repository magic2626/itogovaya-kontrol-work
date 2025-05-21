Объяснение кода Telegram-бота для поиска учебников
Этот код создает Telegram-бота, который помогает пользователям находить и скачивать учебники по различным предметам. Давайте разберем его по частям:

1. Инициализация бота
python
import telebot
from telebot import types
import os

bot = telebot.TeleBot('7873222653:AAHIwCmZf0MYO5fzWBgRsqRf-2pS7VTNm-M')

BOOKS_FOLDER = "textbooks/"
Импортируются необходимые библиотеки: telebot для работы с Telegram API, types для создания кнопок, os для работы с файловой системой.

Создается экземпляр бота с помощью токена (в реальном коде токен следует хранить в переменных окружения или конфигурационном файле).

Определяется папка, где хранятся учебники (textbooks/).

2. Обработка команд /start и /help
python
@bot.message_handler(commands=['start', 'help'])
def send_welcome(message):
    subject_keyboard = types.ReplyKeyboardMarkup(resize_keyboard=True, row_width=2)
    subject_keyboard.add(
        types.KeyboardButton('Алгебра'),
        types.KeyboardButton('Геометрия'),
        types.KeyboardButton('Физика'),
        types.KeyboardButton('Литература')
    )
При командах /start или /help бот отправляет приветственное сообщение.

Создается клавиатура с 4 предметами (Алгебра, Геометрия, Физика, Литература).

Клавиатура автоматически подстраивается под размер экрана (resize_keyboard=True).

3. Обработка текстовых сообщений
python
@bot.message_handler(content_types=['text'])
def handle_text(message):
    user_input = message.text.lower()
Эта функция обрабатывает все текстовые сообщения от пользователя:

Если пользователь пишет "привет", бот отвечает приветствием.

Если пользователь выбирает один из предметов (алгебра, геометрия и т.д.), вызывается функция send_grade_keyboard.

Если предмет не распознан, бот сообщает, что такого предмета нет в базе.

4. Функция для выбора класса
python
def send_grade_keyboard(message, subject_name, subject_code):
    grade_keyboard = types.InlineKeyboardMarkup()
    grade_keyboard.add(
        types.InlineKeyboardButton('7 класс', callback_data=f'{subject_code}_7'),
        types.InlineKeyboardButton('8 класс', callback_data=f'{subject_code}_8'),
        types.InlineKeyboardButton('9 класс', callback_data=f'{subject_code}_9')
    )
Эта функция создает инлайн-клавиатуру с выбором класса (7, 8, 9):

subject_name - русское название предмета (для отображения)

subject_code - английское название (для формирования имени файла)

Каждая кнопка содержит callback_data в формате "предмет_класс" (например, "algebra_7")

5. Обработка нажатий на кнопки
python
@bot.callback_query_handler(func=lambda call: True)
def callback_inline(call):
    subjects = {
        'algebra': 'алгебре',
        'geometry': 'геометрии',
        'physics': 'физике',
        'literature': 'литературе'
    }
Эта функция обрабатывает нажатия на инлайн-кнопки:

Разбирает callback_data на предмет и класс

Проверяет, есть ли такой предмет в базе

Формирует путь к файлу (например, "textbooks/algebra_7.pdf")

Пытается отправить файл пользователю

Если файл не найден, сообщает об этом

6. Запуск бота
python
bot.polling(none_stop=True)
Эта строка запускает бота в режиме постоянного опроса серверов Telegram на наличие новых сообщений.

Как работает бот:
Пользователь запускает бота командой /start

Бот показывает клавиатуру с предметами

Пользователь выбирает предмет → бот показывает кнопки с классами

Пользователь выбирает класс → бот отправляет соответствующий учебник (PDF-файл)

Если файла нет, бот сообщает об этом

Важные замечания:
Токен бота в коде открыт - это небезопасно, лучше хранить его в переменных окружения.

Бот ожидает, что файлы учебников лежат в папке textbooks/ в формате "предмет_класс.pdf" (например, "algebra_7.pdf").

В текущей реализации поддерживаются только 7-9 классы по 4 предметам, но это легко расширить.
