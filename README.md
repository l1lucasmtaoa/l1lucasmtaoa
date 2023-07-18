import telebot
import requests
from io import BytesIO

TOKEN = '5505385627:AAF8BSwDV-B2Cy8I_TpsvQikH5ef6aPkXCY'

bot = telebot.TeleBot(TOKEN, parse_mode="HTML")

@bot.message_handler(commands=['start'])
def send_welcome(message):
    username = message.from_user.username
    response = requests.get("https://telegra.ph/file/0287ed2a0ec273265ded4.jpg")
    photo = BytesIO(response.content)
    mensagem = f"Olá {username}! Aqui é o atendimento automático para vendas de IPTV's.\n\nVocê está aqui para comprar seu acesso?"
    markup = telebot.types.InlineKeyboardMarkup()
    sim_button = telebot.types.InlineKeyboardButton(text="Sim", callback_data="sim")
    nao_button = telebot.types.InlineKeyboardButton(text="Não", callback_data="nao")
    markup.add(sim_button, nao_button)

    bot.send_photo(chat_id=message.chat.id, photo=photo, caption=mensagem, reply_markup=markup)

@bot.callback_query_handler(func=lambda call: True)
def handle_callback_query(call):
    chat_id = call.message.chat.id
    message_id = call.message.message_id
    if call.data == "sim":
        mensagem = "Ótimo! Para ter acesso a filmes, series, tudo isso por um valor simbólico de apenas 29,90. Você deseja ter seu próprio app e assistir seus filmes e séries, da Netflix, da Disney, HBO...?"
        markup = telebot.types.InlineKeyboardMarkup()
        contribuir_button = telebot.types.InlineKeyboardButton(text="Sim comprar", callback_data="contribuir")
        nao_contribuir_button = telebot.types.InlineKeyboardButton(text="Não comprar", callback_data="nao_contribuir")
        markup.row(contribuir_button, nao_contribuir_button)

        bot.edit_message_caption(chat_id=chat_id, message_id=message_id, caption=mensagem, reply_markup=markup)
    elif call.data == "contribuir":
        mensagem = "<b>PARABÉNS VOCÊ ESTÁ A UM PASSO DE TER ACESSO AO MELHOR APP DE ENTRETENIMENTO,COM UMA VIAGEM EMOCIONANTE AO NOSSO APP DE IPTV, PARA CONTINUARMOS FAÇA O PAGAMENTO PELO LINK ABAIXO</b>\n\nDepois de seu pagamento realizado, clique no botão <b>PAGAMENTO REALIZADO</b>"
        markup = telebot.types.InlineKeyboardMarkup()
        comprovante_button = telebot.types.InlineKeyboardButton(text="FAZER PAGAMENTO", url="http://mpago.la/335FksL")
        markup.add(comprovante_button)
        pagamento_realizado_button = telebot.types.InlineKeyboardButton(text="Pagamento Realizado", callback_data="pagamento_realizado")
        markup.row(pagamento_realizado_button)

        bot.edit_message_caption(chat_id=chat_id, message_id=message_id, caption=mensagem, reply_markup=markup)
    elif call.data == "nao":
        mensagem = "Que pena, não sabe o que está perdendo. Se mudar de ideia, é só clicar em 'Sim' novamente."
        markup = telebot.types.InlineKeyboardMarkup()
        sim_button = telebot.types.InlineKeyboardButton(text="Sim", callback_data="sim")
        nao_button = telebot.types.InlineKeyboardButton(text="Não", callback_data="nao")
        markup.row(sim_button, nao_button)

        bot.edit_message_caption(chat_id=chat_id, message_id=message_id, caption=mensagem, reply_markup=markup)
    elif call.data == "nao_contribuir":
        mensagem = "Que pena. Para continuar tendo acesso ao conteúdo exclusivo do nosso grupo, é necessário fazer uma contribuição PIX no valor de 20 reais ou mais. Se mudar de ideia, é só clicar em 'Desejo contribuir' novamente."
        markup = telebot.types.InlineKeyboardMarkup()
        contribuir_button = telebot.types.InlineKeyboardButton(text="Desejo Contribuir", callback_data="contribuir")
        nao_contribuir_button = telebot.types.InlineKeyboardButton(text="Não Contribuir", callback_data="nao_contribuir")
        markup.row(contribuir_button, nao_contribuir_button)

        bot.edit_message_caption(chat_id=chat_id, message_id=message_id, caption=mensagem, reply_markup=markup)
    elif call.data == "pagamento_realizado":
        mensagem = "Qual é a sequência correta?"
        markup = telebot.types.ForceReply()
        bot.send_message(chat_id=chat_id, text=mensagem, reply_markup=markup)

@bot.message_handler(func=lambda message: True, content_types=['text'])
def handle_message(message):
    chat_id = message.chat.id
    if message.reply_to_message and message.reply_to_message.text == "Qual é a sequência correta?":
        resposta_correta = "ABN2C3"
        if message.text == resposta_correta:
            mensagem = "Pagamento realizado! Obrigado pela compra!"
            bot.send_message(chat_id=chat_id, text=mensagem)
        else:
            mensagem = "Resposta incorreta! Tente novamente."
            bot.send_message(chat_id=chat_id, text=mensagem)

bot.polling()
