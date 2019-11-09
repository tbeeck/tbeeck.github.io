---
layout: default
title: "textgenrnn"
description: "Training a neural network on my intimate conversations."
date: 2019-10-07
categories: ["python", "neural-networks", "ml"]
---
# My Adventure With textgenrnn

First, some background: I am not particularly knowledgeable with advanced topics in mathematics. As a result, some of the only experience I have with machine learning are things that work out-of-the-box. For example, [textgenrnn](https://www.purposegames.com/api/1.0/insert-score-and-time).

Textgenrnn is a module built on tensorflow which includes a neural network that can be used to generate text. It makes training and using that neural network really easy, so this was right up my ally for some quick and easy experimentation.

 As soon as I learned about the module, I got to thinking: what data could I get that might produce some interesting text? Sure, I *could* use bigquery to get training data that could be really interesting, but my ultimate goal was to train the model in a way only I could. So, I thought, is there any large volume of data which only I have access to?

 As it turns out, I am continuously producing an extremely large data set every single day: I am constantly texting my girlfriend.

## Gathering and Parsing the Data

 My girlfriend and I use the messaging app Telegram, so the very first query I made was for a way to export my telegram data. As it turns out, you can really easily [export your telegram data and messages from the desktop app.](https://gadgets.ndtv.com/apps/news/telegram-export-chats-notifications-exceptions-passport-encryption-1906903).

 The exported data looked something like this:
```json
 {
 "about": "....{some Telegram notice}",
 "chats": {
  "about": "This page lists all chats from this export.",
  "list": [
   {
    "name": "Bella",
    "type": "personal_chat",
    "id": 00000000000,
    "messages": [
     {
      "id": 1,
      "type": "message",
      "date": "2019-02-01T11:17:00",
      "edited": "1969-12-31T16:00:00",
      "from": "Bella",
      "from_id": 00000000000,
      "text": "Hi Timmy"
     },
     ...
    ]
```

So, I whipped out the python interpreter and loaded the entire thing into memory. Then, I maneuvered around the JSON until I got to the list of messages between Bella and myself.

```python
>>> import json
>>> data = {}
>>> with open('data.json') as f:
>>>   data = json.load(f)
>>> messages = data['chats']['list'][0]['messages']
>>> tim_texts = open('tim.txt', 'w+', encoding='utf8')
>>> for m in messages:
>>>   if m['text'] and m['from'] == 'Tim' and \
          isinstance(m['text'], str):
>>>     tim_texts.write(m['text'] + '\n')
...
```

After doing this for both my own and Bella's texts, I started training the models. I only used one epoch for each one, since the data set was more than 14,000 lines per model, and things were going to take a long time otherwise.

```python
from textgenrnn import textgenrnn

txt = textgenrnn()
txt.train_from_file('bella.txt', new_model=True, num_epochs=1)
txt.generate_to_file('bella_output.txt', n=10)

txt = textgenrnn()
txt.train_from_file('tim.txt', new_model=True, num_epochs=1)
txt.generate_to_file('tim_output.txt', n=10)
```

Sure enough, there were some pretty fun things the AIs both said.

## Here are just some of the fun messages the AI came up with:

Tim:
```text
She problems then I have a lot the aid teall
Okay <3
No I want the did you tomorrow <3
I'm bed see long here and I prodation the day and it is SOOM but want to we me to see anything and I'm grodime :)
You're a good <3 I cun't wait to prokie and shower tean I want tell bella :)
I was then I gotta do the way to be with a lot to do there and I wanna get the doing and it was the 30 make I was that would see and I wanna be her up to get the don the stuff or and I wanna say about the was there are you are and I wanna get the way the was and I'm gettie and I wanno stret you and
I walk to sel the and you're meeting then I know it'll be to projught and I feel see you before falled that tomorrow :(
Finight and shower and I don't work a lot this and I should I was the didn't wanna see you tall do you after the was then I miss you
How do you do that want to have out the saidle and and I should like the don it don't wanna see you tomorrow :)
How's it don't wint to do that take it as and it's at this anyther are you and I'm gonna see you tomorrow :) it's a 120 down down that would be love :)

```

Bella:
```text
U miss you
Just finishe in the of to start I get to see you to be and I all that I was still a make having right baby
No I want to want to see you to an aspest to the mine and I can’t want talk of me and I didn’t home to she stress anything to think what I have till hell a breaking of a bried home and a littlle and I don’t wanna cuddle watch a time everything and I don’t fine me me to se like that worl that was a 
Thank you Timmy
It’s okay I can’t wait to see you too
Yeah I had to see you tomorrow
Do you see you talk to me to the the baby
I’m love babil all that it’s so not like
Nice!!
Oh okay thank you
```

My next goal will be to use this data to have the two AI converse with one another.