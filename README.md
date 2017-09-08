# Rita
Rita a.k.a RIT-assistant is the virtual assistant of RIT.

# Get the code
clone the repostory with
```
git clone https://github.com/RITct/Rita.git
```

# You need 
* [Python3](https://www.google.co.in/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwj-gYDY0KfVAhWGq5QKHb-oB9oQFggnMAA&url=https%3A%2F%2Fwww.python.org%2Fdownload%2Freleases%2F3.0%2F&usg=AFQjCNF6zKf7aO02xzo_mGRre5Zbv7ihVA)
* [PyTorch](https://www.google.co.in/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjR0dTMnJjVAhUBLZQKHTv2DRkQFgglMAA&url=http%3A%2F%2Fpytorch.org%2F&usg=AFQjCNEXSfCCJoYl_S4Utq27TyMxwyWZsg)
* [Flask](https://www.google.co.in/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiR-qGLnZjVAhWMQpQKHTAvCR4QFgglMAA&url=http%3A%2F%2Fflask.pocoo.org%2F&usg=AFQjCNHCF6gYMbnkUKtJl-u3lzTeLt-61A)
* [Requests](http://docs.python-requests.org/en/master/)
* [flask_wtf](https://www.google.co.in/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwiIhJKU1p3VAhVGtI8KHWUcDHIQFggnMAA&url=https%3A%2F%2Fflask-wtf.readthedocs.io%2F&usg=AFQjCNFaFBlKvOFtw3CNKT2uSCTlzkZD9Q)
# How to run
First Run
```
bash setup.sh
```
for training the models.
</br>
A local interface can be run like this
</br>

```
python3 rita.py
```
Go to,
```
http://127.0.0.1:5000/test
```
in the browser. Tada !

</br>
</br>

![RIT-Assistant](rita.png?raw=true "RITA")

# Working
Currently building Rita as a facebook messenger bot in [python](www.python.org) [flask](flask.pocoo.org) based on this [tutorial](https://blog.hartleybrody.com/fb-messenger-bot/).
</br>
Message user send is fetched.
```
data = request.get_json()
```
This will be in json format as send by [facebook API](https://developers.facebook.com/docs/messenger-platform/webhook-reference/message).
```
{
  "sender":{
    "id":"USER_ID"
  },
  "recipient":{
    "id":"PAGE_ID"
  },
  "timestamp":1458692752478,
  "message":{
    "mid":"mid.1457764197618:41d102a3e1ae206a38",
    "text":"hello, world!",
    "quick_reply": {
      "payload": "DEVELOPER_DEFINED_PAYLOAD"
    }
  }
}    
```
The message is parsed and extracted.
```
message_text = message_event["message"]["text"]
```
This message is processed and reply is generated using neural networks.
```
reply = process_msg(message_text)
```
This is how things work inside process_msg(),
```
def process_msg(message_text):
    intent = action_predict(str(message_text)) #predicting action
    if intent != "none": #if the message is a command
        k = dsl(intent)
        reply = k.generate()
        return reply
    else: #if the message is just chitchat
        reply = reply_predict(message_text)
        return reply
        
```
The predict_action() function output an action to be performed or "none" if no intent recognised. If intent is found then action is executed in dsl.py and a reply is generated. If predict_action() return "none", that means its a normal conversation like,
</br>
"hello how are you ?"
</br>
Another model called seqtoseq will generate a reply for the question. 
</br>
We use two neural network models in Rita
## 1. Intent Recognition model
```
intent = predict_action(str(message_text))
```
Which will predict the action to be done by the bot from the message.
For example say "tell me about RIT", for this question bot may reply with link to RIT website.
This is an example tutorial for how to build an [intent recogniser](https://github.com/GopikrishnanSasikumar/Text_Classifier-pytorch).
The dataset used to train this model is stored in
```
 action_dataset.json
```
The intent recognition model can be trained by running
```
python3 action_train.py
```
## 2. SeqtoSeq model
This model is used for implementing normal conversations for questions like 
"hello how are you ?"
Its a neural network model that will translate the question to answer like this.
![alt text](https://camo.githubusercontent.com/242210d7d0151cae91107ee63bff364a860db5dd/687474703a2f2f6936342e74696e797069632e636f6d2f333031333674652e706e67)
</br>
Seqtoseq model can be trained by running,
```
python3 seqtoseq_train.py
```
SeqtoSeq model in pytorch can be implemented like [this](http://pytorch.org/tutorials/intermediate/seq2seq_translation_tutorial.html#sphx-glr-intermediate-seq2seq-translation-tutorial-py).

# Tasks
## 1. chitchat dataset
AI assistants like google assistant are trained with huge conversatonal dataset extracted from email and google plus chat data collections. Those datasets are not available for public. Rita need such a dataset for training the coversational model.
```
chitchat_dataset.json
```
is created for collecting that dataset. It contain,
```
question:
```
and 
```
answer:
```
for each sample.
</br>
</br>
unleash your sarcasm level, contribute in the dataset !
</br>
Add more questions and answers in 
```
seqtoseq_dataset.json
```
 
like this,
 ```

    {
        "question": "your question",
        "answer": "its answer"
    },

```
## 2. Action_dataset
Make a list of actions to be performed by Rita.Create dataset for those actions inside 
```
action_dataset.json 
```
in the form like this,
```
"intent": "website","sentence": "open college website"
"intent":"website", "sentence":"show me RIT website"
"intent":"website", "sentence":"give me more information"
"intent":"website", "sentence":"tell me more about RIT"
```
</br>
Then train the models again by running 
```
bash setup.sh
```

## See you on Ritu :heart:
