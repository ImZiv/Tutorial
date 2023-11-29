# Cloud9安裝部分
``` txt
創建資料夾
mkdir python

到指定資料夾
cd python

安裝line-bot-sdk(因為官方網站建議要使用python 3.8以上)
pip3 install -t python/ line-bot-sdk

刪除urllib3之後安裝舊版的
pip3 install -t python/ urllib3==1.26

安裝openai
pip3 install -t python/ openai

因為最近好像有改版，他建議我們升級(https://github.com/openai/openai-python/discussions/742)
pip install --upgrade openai

然後壓縮資料夾
zip -r layer.zip python

推到lambda去
aws lambda publish-layer-version --layer-name openaiAndlinebotLayer --zip-file fileb://layer.zip --compatible-runtime python3.8
```

--- 

# 程式碼部分
``` python
import os
import json
from openai import OpenAI
from linebot import LineBotApi, WebhookHandler
from linebot.exceptions import InvalidSignatureError
from linebot.models import MessageEvent, TextMessage, TextSendMessage

client = OpenAI(api_key=os.environ['openai_key'])
line_bot_api = LineBotApi(os.environ['line_bot_access_key'])
handler = WebhookHandler(os.environ['line_bot_secret_key'])

def lambda_handler(event, context):
    # line回傳的簽名
    signature = event['headers']['x-line-signature']
    body = event['body']
    # 因為是字串的json格式，所以要load近來做操作
    parse_body_json = json.loads(body)
    
    # 使用者傳進來的訊息
    get_input_text = parse_body_json["events"][0]["message"]["text"]
    handler.handle(body, signature)
    # 取得這個對話的token
    reply_token = parse_body_json['events'][0]['replyToken']
    # 設定openai的模型
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "你是一個解決疑難雜症的AI"},
            {"role": "user", "content": get_input_text},
        ]
    )
    response_text = response.choices[0].message.content
    # 設定要傳送的訊息
    text_message = TextSendMessage(text=response_text)
    # 送出訊息
    line_bot_api.reply_message(reply_token,text_message)
    return 'OK'
```
