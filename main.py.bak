import telebot
import sqlite3

from telebot import types

with open("token.txt", 'r', encoding="utf-8") as file:
    token = file.read()

bot = telebot.TeleBot(token)

username = None
fio_delete = None
fio_14 = None
tel_14 = None
address_14 = None
datetime_14 = None
reason = None
place = None
fio = None
date_of_birth = None
series_number = None
date_of_issue = None
who_issued = None
division_code = None
place_of_birth = None
country = None
tel = None
email = None
place_of_residence = None
date_of_registration = None
actual_housing = None
marital_status = None
father = None
mother = None
address = None

@bot.message_handler(commands=['start'])
def start(message):
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    btn1 = types.KeyboardButton('/point1')
    btn2 = types.KeyboardButton('/point2')
    markup.row(btn1, btn2)
    btn3 = types.KeyboardButton('/point3')
    markup.row(btn3)
    bot.send_message(message.chat.id, '<strong>Укажите, с какой целью вы хотите воспользоваться ботом:</strong>\n/point1 - достижение 14-летнего возраста\n/point2 - замена паспорта\n/point3 - отмена заявки', parse_mode='html', reply_markup=markup)

@bot.message_handler(commands=['point1'])
def on_14(message):
    bot.send_message(message.chat.id, 'Для получения паспорта нужно записаться на прием в МФЦ или в подразделение МВД. В МФЦ подать заявку можно не позднее 90 дней после наступления 14-летия, иначе только в МВД')
    bot.send_message(message.chat.id, 'Для записи на прием вам нужно предоставить следующие данные')
    conn = sqlite3.connect('the_first_passport.sql')
    cur = conn.cursor()
    cur.execute('CREATE TABLE IF NOT EXISTS users_first (id int auto_increment primary key, username varchar(50), fio varchar(150), tel varchar(30), address varchar(200), data_vremya datetime)')
    conn.commit()
    cur.close()
    conn.close()
    bot.send_message(message.chat.id, 'Введите свое полное ФИО')
    bot.register_next_step_handler(message, on_fio_14)

def on_fio_14(message):
    global fio_14
    fio_14 = message.text.strip()
    bot.send_message(message.chat.id, 'Введите свой номер телефона для связи')
    bot.register_next_step_handler(message, on_tel_14)

def on_tel_14(message):
    global tel_14
    tel_14 = message.text.strip()
    markup = types.InlineKeyboardMarkup()
    markup.add(types.InlineKeyboardButton('Перейти на сайт с адресами МФЦ', url='https://моидокументы.рф/search/mfc'))
    markup.add(types.InlineKeyboardButton('Перейти на сайт с адресами МВД', url='https://мвд.рф/contacts/sites'))
    bot.send_message(message.chat.id, 'Введите адрес нужного вам МФЦ или МВД (адреса можно посмотреть по ссылкам ниже)', reply_markup=markup)
    bot.register_next_step_handler(message, on_address_14)

def on_address_14(message):
    global address_14
    address_14 = message.text.strip()
    bot.send_message(message.chat.id, 'Введите дату и время в следующем формате: ГГГГ-ММ-ДД ЧЧ:ММ:СС')
    bot.register_next_step_handler(message, on_datetime_14)

def on_datetime_14(message):
    global datetime_14
    datetime_14 = message.text.strip()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    btn1 = types.KeyboardButton('Да')
    btn2 = types.KeyboardButton('Нет')
    markup.row(btn1, btn2)
    bot.send_message(message.chat.id, 'Вы подтверждаете запись?', reply_markup=markup)
    bot.register_next_step_handler(message, on_confirmation_14)

def on_confirmation_14(message):
    global username
    username = message.from_user.username
    if message.text == 'Да':
        bot.send_message(message.chat.id, 'Заявка принята! Администратор рассмотрит заявку и свяжется с вами в ближайшее время')
        bot.send_message(message.chat.id, '<strong>На прием возьмите с собой:</strong>\n🔹Свидетельство о рождении\n🔹Документ, удостоверяющий гражданство РФ, если нет соответствующей отметки в свидетельстве о рождении\n' +
        '🔹Два чёрно-белых или цветных фото размером 35×45 мм\n🔹Квитанция об уплате госпошлины в размере 300 ₽. Оплата обязательна, приносить квитанцию — необязательно',
        parse_mode='html')
        conn = sqlite3.connect('the_first_passport.sql')
        cur = conn.cursor()
        cur.execute(f"INSERT INTO users_first (username, fio, tel, address, data_vremya) VALUES ('%s', '%s', '%s', '%s', '%s')" % (username, fio_14, tel_14, address_14, datetime_14))
        conn.commit()
        cur.close()
        conn.close()
        bot.stop_bot()
    elif message.text == 'Нет':
        bot.send_message(message.chat.id, 'Заявка отменена! Спасибо, что обратились к нам')
        bot.stop_bot()

@bot.message_handler(commands=['point3'])
def on_delete(message):
    conn = sqlite3.connect('cancellation_sheet.sql')
    cur = conn.cursor()
    cur.execute('CREATE TABLE IF NOT EXISTS users_del (id int auto_increment primary key, username varchar(50), fio varchar(150))')
    conn.commit()
    cur.close()
    conn.close()
    bot.send_message(message.chat.id, 'Введите ФИО, которое вы указывали при заявке')
    bot.register_next_step_handler(message, on_fio_delete)

def on_fio_delete(message):
    global fio_delete, username
    fio_delete = message.text.strip()
    username = message.from_user.username
    conn = sqlite3.connect('cancellation_sheet.sql')
    cur = conn.cursor()
    cur.execute(f"INSERT INTO users_del (username, fio) VALUES ('%s', '%s')" % (username, fio_delete))
    conn.commit()
    cur.close()
    conn.close()
    bot.send_message(message.chat.id, 'Принято! В ближайшее время с вами свяжется администратор')
    bot.stop_bot()

@bot.message_handler(commands=['admin'])
def administrator(message):
    bot.send_message(message.chat.id, 'Введите пароль')
    bot.register_next_step_handler(message, on_password)

def on_password(message):
    if message.text != '111':
        bot.send_message(message.chat.id, 'Пароль неверный, повторите попытку')
        bot.register_next_step_handler(message, on_password)
    else:
        bot.send_message(message.chat.id, f'С возвращением, {message.from_user.first_name}!')
        markup = types.ReplyKeyboardMarkup()
        markup.add(types.KeyboardButton('Заявки на первое получение паспорта'))
        markup.add(types.KeyboardButton('Заявки на повторное получение паспорта'))
        markup.add(types.KeyboardButton('Заявки на удаление'))
        bot.send_message(message.chat.id, 'Списки пользователей', reply_markup=markup)
        bot.register_next_step_handler(message, on_results)

def on_results(message):
    if message.text == 'Заявки на первое получение паспорта':
        conn = sqlite3.connect('the_first_passport.sql')
        cur = conn.cursor()
        cur.execute('SELECT * FROM users_first')
        users_first = cur.fetchall()
        info1 = ''
        for us in users_first:
            info1 += f'Имя пользователя: {us[1]}, ФИО: {us[2]}, Телефон: {us[3]}, Адрес для подачи заявления: {us[4]}, Дата и время записи: {us[5]}\n'
        cur.close()
        conn.close()
        bot.send_message(message.chat.id, info1)
    elif message.text == 'Заявки на повторное получение паспорта':
        conn = sqlite3.connect('second_passport.sql')
        cur = conn.cursor()
        cur.execute('SELECT * FROM users_second')
        users_first = cur.fetchall()
        info2 = ''
        for us in users_first:
            info2 += f'Имя пользователя: {us[1]}, Причина: {us[2]}, Место получения: {us[3]}, ФИО: {us[4]}, Дата рождения: {us[5]}, Серия и номер: {us[6]}, Дата выдачи: {us[7]}, Кем выдан: {us[8]}, Код подразделения: {us[9]}, Место рождения: {us[10]}, Страна: {us[11]}, Телефон: {us[12]}, Почта: {us[13]}, Место жительства: {us[14]}, Дата регистрации: {us[15]}, Фактическое проживание: {us[16]}, Статус: {us[17]}, Отец: {us[18]}, Мать: {us[19]}, Адрес: {us[20]}\n'
        cur.close()
        conn.close()
        bot.send_message(message.chat.id, info2)
    elif message.text == 'Заявки на удаление':
        conn = sqlite3.connect('cancellation_sheet.sql')
        cur = conn.cursor()
        cur.execute('SELECT * FROM users_del')
        users_delete = cur.fetchall()
        info3 = ''
        for us in users_delete:
            info3 += f'Имя пользователя: {us[1]}, ФИО: {us[2]}\n'
        cur.close()
        conn.close()
        bot.send_message(message.chat.id, info3)
    bot.register_next_step_handler(message, on_results)

@bot.callback_query_handler(func=lambda ccall: True)
def call_point2(ccall):
    conn = sqlite3.connect('second_passport.sql')
    cur = conn.cursor()
    cur.execute('SELECT * FROM users_second')
    users_first = cur.fetchall()
    info2 = ''
    for us in users_first:
        info2 += f'Имя пользователя: {us[1]}, Причина: {us[2]}, Место получения: {us[3]}, ФИО: {us[4]}, Дата рождения: {us[5]}, Серия и номер: {us[6]}, Дата выдачи: {us[7]}, Кем выдан: {us[8]}, Код подразделения: {us[9]}, Место рождения: {us[10]}, Страна: {us[11]}, Телефон: {us[12]}, Почта: {us[13]}, Место жительства: {us[14]}, Дата регистрации: {us[15]}, Фактическое проживание: {us[16]}, Статус: {us[17]}, Отец: {us[18]}, Мать: {us[19]}, Адрес: {us[20]}\n'
    cur.close()
    conn.close()
    bot.send_message(ccall.message.chat.id, info2)

@bot.message_handler(commands=['point2'])
def on_replacement(message):
    conn = sqlite3.connect('second_passport.sql')
    cur = conn.cursor()
    cur.execute('CREATE TABLE IF NOT EXISTS users_second (id int auto_increment primary key, username varchar(50), reason varchar(150), place varchar(100), fio varchar(150), data_birth date, ser_num varchar(15), data_issue date, who varchar(150), code varchar(10), place_birth varchar(50), country varchar(50), tel varchar(30), email varchar(100), place_res varchar(200), date_reg date, act_hous varchar(15), mar_status varchar(50), father varchar(150), mother varchar(150), adres varchar(150))')
    conn.commit()
    cur.close()
    conn.close()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    btn1 = types.KeyboardButton('Достижение 20 или 45 лет')
    btn2 = types.KeyboardButton('Изменение фамилии, имени или отчества')
    markup.row(btn1, btn2)
    btn3 = types.KeyboardButton('Непригодность к использованию')
    btn4 = types.KeyboardButton('Ошибка в паспорте')
    markup.row(btn3, btn4)
    btn5 = types.KeyboardButton('Изменение внешности')
    btn6 = types.KeyboardButton('Изменение даты и места рождения')
    markup.row(btn5, btn6)
    bot.send_message(message.chat.id, 'По какой причине нужно поменять паспорт?', reply_markup=markup)
    bot.register_next_step_handler(message, on_reason)

def on_reason(message):
    global reason
    reason = message.text.strip()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    btn1 = types.KeyboardButton('По месту прописки')
    btn2 = types.KeyboardButton('По месту проживания без прописки')
    btn3 = types.KeyboardButton('По месту временной регистрации')
    markup.row(btn1, btn2, btn3)
    bot.send_message(message.chat.id, '<strong>Пометка:</strong>\n🔹По месту прописки — в подразделении, обслуживающем адрес, по которому вы постоянно зарегистрированы. Этот адрес указан в штампе о регистрации в паспорте. Срок оформления паспорта 5 рабочих дней\n' +
                     '🔹По месту проживания без прописки — там, где вы фактически живёте, но нет постоянной или временной регистрации. Выбирайте этот вариант, если хотите получить паспорт в подразделении, которое ближе к работе, в другом городе или регионе. Срок оформления паспорта 5 рабочих дней\n' +
                     '🔹По месту временной регистрации — в подразделении, обслуживающем адрес, по которому оформлена временная регистрация. Срок оформления паспорта 5 рабочих дней\n',
                     parse_mode='html')
    bot.send_message(message.chat.id, 'Где вы хотите получить паспорт?', reply_markup=markup)
    bot.register_next_step_handler(message, on_place)

def on_place(message):
    global place
    place = message.text.strip()
    bot.send_message(message.chat.id, 'Откройте паспорт и введите данные, которые сейчас попросят указать')
    bot.send_message(message.chat.id, 'Введите фамилию, имя и отчество')
    bot.register_next_step_handler(message, on_fio)

def on_fio(message):
    global fio
    fio = message.text.strip()
    bot.send_message(chat_id='-967051920', text=fio)
    bot.send_message(message.chat.id, 'Введите дату рождения в следующем формате: ГГГГ-ММ-ДД')
    bot.register_next_step_handler(message, on_date_of_birth)

def on_date_of_birth(message):
    global date_of_birth
    date_of_birth = message.text.strip()
    bot.send_message(message.chat.id, 'Введите серию и номер')
    bot.register_next_step_handler(message, on_series_number)

def on_series_number(message):
    global series_number
    series_number = message.text.strip()
    bot.send_message(message.chat.id, 'Введите дату выдачи паспорта в следующем формате: ГГГГ-ММ-ДД')
    bot.register_next_step_handler(message, on_date_of_issue)

def on_date_of_issue(message):
    global date_of_issue
    date_of_issue = message.text.strip()
    bot.send_message(message.chat.id, 'Введите, кем выдан паспорт')
    bot.register_next_step_handler(message, on_who_issued)

def on_who_issued(message):
    global who_issued
    who_issued = message.text.strip()
    bot.send_message(message.chat.id, 'Введите код подразделения')
    bot.register_next_step_handler(message, on_division_code)

def on_division_code(message):
    global division_code
    division_code = message.text.strip()
    bot.send_message(message.chat.id, 'Введите место рождения')
    bot.register_next_step_handler(message, on_place_of_birth)

def on_place_of_birth(message):
    global place_of_birth
    place_of_birth = message.text.strip()
    bot.send_message(message.chat.id, 'Отлично, первый этап пройден успешно! Если вы где-то ошиблись, нажмите /point2 и введите информацию заново. Ваши данные будут перезаписаны')
    bot.send_message(message.chat.id, 'Идем дальше.\n Введите страну, указанную в вашем свидетельстве о рождении. Если родились в одной из республик СССР, указывайте СССР')
    bot.register_next_step_handler(message, on_country)

def on_country(message):
    global country
    country = message.text.strip()
    bot.send_message(message.chat.id, 'Введите контактный телефон')
    bot.register_next_step_handler(message, on_tel)

def on_tel(message):
    global tel
    tel = message.text.strip()
    bot.send_message(message.chat.id, 'Введите адрес электронной почты')
    bot.register_next_step_handler(message, on_email)

def on_email(message):
    global email
    email = message.text.strip()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    markup.add(types.KeyboardButton('Нет постоянной регистрации'))
    bot.send_message(message.chat.id, 'Введите адрес места жительства (прописки). Адрес постоянной регистрации нужен для отправки заявления', reply_markup=markup)
    bot.register_next_step_handler(message, on_place_of_residence)

def on_place_of_residence(message):
    global place_of_residence
    place_of_residence = message.text.strip()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    markup.add(types.KeyboardButton('Нет постоянной регистрации'))
    bot.send_message(message.chat.id, 'Введите дату регистрации места жительства (прописки) в следующем формате: ГГГГ-ММ-ДД. Дату регистрации можно найти на штампе о регистрации на стр. 5-12 паспорта', reply_markup=markup)
    bot.register_next_step_handler(message, on_date_of_registration)

def on_date_of_registration(message):
    global date_of_registration
    date_of_registration = message.text.strip()
    bot.send_message(message.chat.id, 'Введите адрес фактического проживания. Адрес фактического проживания нужен для отправки заявления')
    bot.register_next_step_handler(message, on_actual_housing)

def on_actual_housing(message):
    global actual_housing
    actual_housing = message.text.strip()
    bot.send_message(message.chat.id, 'Требования к фото для подачи заявления')
    file = open('./photo1.png', 'rb')
    bot.send_photo(message.chat.id, file)
    file = open('./photo2.png', 'rb')
    bot.send_photo(message.chat.id, file)
    file = open('./photo3.png', 'rb')
    bot.send_photo(message.chat.id, file)
    file = open('./photo4.png', 'rb')
    bot.send_photo(message.chat.id, file)
    file = open('./photo5.png', 'rb')
    bot.send_photo(message.chat.id, file)
    bot.send_message(message.chat.id, 'Фото, отправленное вами сейчас, не будет в паспорте. Оно останется в личном деле')
    bot.register_next_step_handler(message, on_photo)

@bot.message_handler(content_types=['photo'])
def on_photo(message):
    bot.forward_message(-967051920, message.chat.id, message.id)
    bot.reply_to(message, 'Принято')
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    btn1 = types.KeyboardButton('Никогда не была замужем/Никогда не был женат')
    btn2 = types.KeyboardButton('Замужем/Женат')
    markup.row(btn1, btn2)
    btn3 = types.KeyboardButton('Разведена/Разведен')
    btn4 = types.KeyboardButton('Вдова/Вдовед')
    markup.row(btn3, btn4)
    bot.send_message(message.chat.id, 'Введите ваше семейное положение', reply_markup=markup)
    bot.register_next_step_handler(message, on_marital_status)

def on_marital_status(message):
    global marital_status
    marital_status = message.text.strip()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    markup.add(types.KeyboardButton('Нет сведений об отце'))
    bot.send_message(message.chat.id, 'Укажите сведения о родителях, как в вашем свидетельстве о рождении')
    bot.send_message(message.chat.id, 'Укажите сведения о об отце', reply_markup=markup)
    bot.register_next_step_handler(message, on_father)

def on_father(message):
    global father
    father = message.text.strip()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    markup.add(types.KeyboardButton('Нет сведений о матери'))
    bot.send_message(message.chat.id, 'Укажите сведения о матери', reply_markup=markup)
    bot.register_next_step_handler(message, on_mother)

def on_mother(message):
    global mother
    mother = message.text.strip()
    markup = types.InlineKeyboardMarkup()
    markup.add(types.InlineKeyboardButton('Перейти на сайт с адресами МФЦ', url='https://моидокументы.рф/search/mfc'))
    markup.add(types.InlineKeyboardButton('Перейти на сайт с адресами МВД', url='https://мвд.рф/contacts/sites'))
    bot.send_message(message.chat.id, 'Введите адрес нужного вам МФЦ или МВД (адреса можно посмотреть по ссылкам ниже)', reply_markup=markup)
    bot.register_next_step_handler(message, on_end)

def on_end(message):
    global address
    address = message.text.strip()
    markup = types.ReplyKeyboardMarkup(resize_keyboard=True, one_time_keyboard=True)
    btn1 = types.KeyboardButton('Да')
    btn2 = types.KeyboardButton('Нет')
    markup.row(btn1, btn2)
    bot.send_message(message.chat.id, 'Вы подтверждаете запись?', reply_markup=markup)
    bot.register_next_step_handler(message, on_confirmation)

def on_confirmation(message):
    global username
    username = message.from_user.username
    if message.text == 'Да':
        bot.send_message(message.chat.id, 'Заявка принята! Администратор рассмотрит заявку и свяжется с вами в ближайшее время и дополнительно проинформирует')
        conn = sqlite3.connect('second_passport.sql')
        cur = conn.cursor()
        cur.execute(f"INSERT INTO users_second (username, reason, place, fio, data_birth, ser_num, data_issue, who, code, place_birth, country, tel, email, place_res, date_reg, act_hous, mar_status, father, mother, adres) VALUES ('%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s', '%s')" % (username, reason, place, fio, date_of_birth, series_number, date_of_issue, who_issued, division_code, place_of_birth, country, tel, email, place_of_residence, date_of_registration, actual_housing, marital_status, father, mother, address))
        conn.commit()
        cur.close()
        conn.close()
        bot.stop_bot()
    elif message.text == 'Нет':
        bot.send_message(message.chat.id, 'Заявка отменена! Спасибо, что обратились к нам')
        bot.stop_bot()

bot.polling(none_stop=True)