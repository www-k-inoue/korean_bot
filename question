from flask import Flask, request, abort
from linebot import (
    LineBotApi, WebhookHandler
)
from linebot.exceptions import (
    InvalidSignatureError
)
from linebot.models import (
    MessageEvent, TextMessage, TextSendMessage,
)

import gspread
from oauth2client.service_account import ServiceAccountCredentials
import random

app = Flask(__name__)

line_bot_api = LineBotApi('SJlzxyt6mX4/exgJCxnu904+0sJIuzUOwDDFEFjRmgjEAbYyiNP8j2FZheC0l/h/BwffolQNRd9eyCGal/MDoOO316md2FYFW1U8HDy8lABT1/1YQ74abBLi68U6QL8Fluk+I2EyzU85smohXA0SWwdB04t89/1O/w1cDnyilFU=')
handler = WebhookHandler('31082e2656115cd68f785c34a9e450f7')

@app.route("/callback", methods=['POST'])
def callback():
    signature = request.headers['X-Line-Signature']

    body = request.get_data(as_text=True)
    app.logger.info("Request body: " + body)

    try:
        handler.handle(body, signature)
    except InvalidSignatureError:
        abort(400)

    return 'OK'

@handler.add(MessageEvent, message=TextMessage)
def handle_message(event):
    text = event.message.text
    if text == '問題':
        # ここで問題を取得して送信
        question = get_question_from_spreadsheet()
        line_bot_api.reply_message(
            event.reply_token,
            TextSendMessage(text=question)
        )
    elif text.startswith('回答:'):
        # ここで回答を処理
        answer = text.split(':')[1]
        result = check_answer(answer)
        line_bot_api.reply_message(
            event.reply_token,
            TextSendMessage(text=result)
        )



def get_question_from_spreadsheet():
    # Google APIの認証情報
    scope = ['https://www.googleapis.com/auth/spreadsheets.readonly']
    creds = ServiceAccountCredentials.from_json_keyfile_name('korean-3choice-bot-021e49bdf4b4.json', scope)
    client = gspread.authorize(creds)

    # スプレッドシートを開く
    sheet = client.open('韓国観光クイズ').sheet1

    # データを取得する
    questions = sheet.get_all_records()

    # ランダムに問題を選択
    selected_question = random.choice(questions)

    return selected_question[0]  # 問題が格納されている列名


def check_answer(answer):
    # ユーザーの回答をチェックする処理
    pass

if __name__ == "__main__":
    app.run()
