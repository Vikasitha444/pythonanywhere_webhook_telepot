# pythonanywhere_webhook_telepot
In this repository we gonna see how to setup a webhook to the telegram bot you've created with telepot. This is one of easiest way to set the webhook. Copy this code without changing anything but your telegram bot token, A random Number and your pythonanywhere Server URL.





from flask import Flask, request
import telepot
import urllib3

proxy_url = "http://proxy.server:3128"
telepot.api._pools = {
   'default': urllib3.ProxyManager(proxy_url=proxy_url, num_pools=3, maxsize=10, retries=False, timeout=30),
}
telepot.api._onetime_pool_spec = (urllib3.ProxyManager, dict(proxy_url=proxy_url, num_pools=1, maxsize=1, retries=False, timeout=30))

secret = "A_SECRET_NUMBER"
bot = telepot.Bot('YOUR_AUTHORIZATION_TOKEN')
bot.setWebhook("https://YOUR_PYTHONANYWHERE_USERNAME.pythonanywhere.com/{}".format(secret), max_connections=1)

app = Flask(__name__)

@app.route('/{}'.format(secret), methods=["POST"])
def telegram_webhook():
   update = request.get_json()
   if "message" in update:
       chat_id = update["message"]["chat"]["id"]
       if "text" in update["message"]:
           text = update["message"]["text"]
           bot.sendMessage(chat_id, "From the web: you said '{}'".format(text))
       else:
           bot.sendMessage(chat_id, "From the web: sorry, I didn't understand that kind of message")
   return "OK"
