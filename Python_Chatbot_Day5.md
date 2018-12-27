# Python Chatbot 5일차
## I. 환경 변수
Telegram token을 환경 변수로 지정하여 사용
1. git bash 켠다.
```
code ~/.bashrc
```

2. VS code 에서 token 저장

```python
export TELEGRAM_TOKEN="<token>"
```

3. git bash 에서 리로드

```
source .bashrc
```

4. git bash 에서 확인

```
cat .bashrc
```

### (1) Telegram 적용

```python
# get_env.py
import os
import requests
from pprint import pprint as pp

name = os.getenv('NAME')
token = os.getenv('TELEGRAM_TOKEN')
method = "getUpdates"

url = f"https://api.telegram.org/bot{token}/{method}"

res = requests.get(url)
doc = res.json()
# json.loads(json) => python dictionary
chat_id = doc['result'][0]['message']['chat']['id']

method = "sendMessage"
url = f"https://api.telegram.org/bot{token}/{method}"
requests.get(f"{url}?chat_id={chat_id}&text={name}")

# ?chat_id={chat_id}&text={name}
# string 수술(interpolation)
# 1. f-string
# print(f"Hello, {name}")
# 2. format()
# print("Hello, {}".format(name))
```

### (2) 함수

함수(function) => 모듈화(기능별로 분리)

0. 어떻게 분리할지 생각한다.
1. 입력을 무엇을 받을지 생각한다.(정의한다.)
2. 결과가 어떻게 되는지 생각한다.(정의한다.)
3. 입력 -> 결과 과정을 생각한다.

```python
import os
import requests
from pprint import pprint as pp

token = os.getenv('TELEGRAM_TOKEN')
msg = input()

def getID(token): # => chat_id
    url = f"https://api.telegram.org/bot{token}/getUpdates"
    res = requests.get(url)
    doc = res.json()
    # json.loads(json) => python dictionary
    chat_id = doc['result'][0]['message']['chat']['id']
    return chat_id

def sendMessage(chat_id,token,msg): # => 텔레그램 메시지를 보냄
    url = f"https://api.telegram.org/bot{token}/sendMessage?chat_id={chat_id}&text={msg}"
    requests.get(url)
    print(f"{msg} 을(를) {chat_id} 님에게 보냈습니다.")
    return

sendMessage(getID(token),token,msg)
```

## II. POST

### (1) Clova Face Recognition

GET : 가져오다(Default)

* 정보를 가져오다

POST : 게시하다

* 정보를 게시하다, 작성하다, 데이터를 보내다

1. .bashrc 파일에 환경변수 저장

```
export NAVER_ID="<Client ID>"
export NAVER_SECRET="<Client Secret>"
```

2. python 코드 작성

```python
import os
import sys
import requests
client_id = os.getenv('NAVER_ID')
client_secret = os.getenv('NAVER_SECRET')
# url = "https://openapi.naver.com/v1/vision/face" // 얼굴감지
url = "https://openapi.naver.com/v1/vision/celebrity" # // 유명인 얼굴인식
files = {'image': open('<IMG_NAME>', 'rb')}
headers = {'X-Naver-Client-Id': client_id, 'X-Naver-Client-Secret': client_secret }
response = requests.post(url,  files=files, headers=headers)
rescode = response.status_code

name = response.json()["faces"][0]["celebrity"]["value"]
if(rescode==200):
    print (response.text)
    # print (name)
else:
    print("Error Code:" + rescode)
```

### (2) POST 이해 예제

```python
# app.py
from flask import Flask, render_template, request

app = Flask(__name__)

@app.route('/') # == @app.route('/', methods=['GET'])
def index():
    return render_template('index.html')
    
@app.route('/signup', methods=['POST'])
def signup():
    email = request.form.get('email')
    password = request.form.get('password')
    
    adminEmail = "qwer@qwer.com"
    adminPassword = "12341234"
    
    # 만약에 회원가입한 회원이 admin일 경우
    #   "관리자님 환영합니다."
    # 아닐 경우
    #   아이디만 맞는 경우
    #       "관리자님 비번이 틀렸어요. 좀만 더 생각해보세요."
    #   아이디도 틀린 경우
    #       "꺼지셈"
    if email == adminEmail and password == adminPassword:
        msg = "관리자님 환영합니다."
    elif email == adminEmail and password != adminPassword:
        msg = "관리자야 비번 잘 생각해보렴."
    else:
        msg = "끄지셈"
    
    return render_template('signup.html', msg=msg)
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>회원가입</h1>
    <form action="/signup" method="POST">
        이메일 : <input type="text" name="email">
        비밀번호 : <input type="password" name="password">
        <input type="submit">
    </form>
</body>
</html>
```

```html
<!-- signup.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>{{msg}}</h1>
</body>
</html>
```

### (3) Telegram 메아리 + Clova

```python
# app.py
from flask import Flask, request
import os
import requests
from pprint import pprint as pp
import random

app = Flask(__name__)

token = os.getenv('TELEGRAM_TOKEN')
base_url = "https://api.hphk.io/telegram" # "https://api.telegram.org"
my_url = "https://webhook-hoik92.c9users.io/"

naver_id = os.getenv('NAVER_ID')
naver_secret = os.getenv('NAVER_SECRET')

# 웹훅을 통해 정보가 들어올 route
@app.route('/{}'.format(token), methods=['POST'])
def telegram():
    doc = request.get_json()
    pp(doc)
    # 들어온 메시지를 메아리로 말하는 챗봇
    chat_id = doc['message']['chat']['id']
    text = doc['message'].get('text')
    
    # if text == "로또":
    #     lotto = str(random.sample(range(1,46), 6))
    #     url = "{}/bot{}/sendMessage?chat_id={}&text={}".format(base_url, token, chat_id, lotto)
    # else:
    #     url = "{}/bot{}/sendMessage?chat_id={}&text={}".format(base_url, token, chat_id, text)
    
    img = False
    
    if doc.get('message').get('photo') is not None:
        img = True
    
    if img:
        file_id = doc.get('message').get('photo')[-1].get('file_id')
        file = requests.get("{}/bot{}/getFile?file_id={}".format(base_url, token, file_id))
        file_url = "{}/file/bot{}/{}".format(base_url, token, file.json().get('result').get('file_path'))
        
        # 네이버로 요청
        res = requests.get(file_url, stream=True)
        clova_res = requests.post('https://openapi.naver.com/v1/vision/celebrity',
            headers={
                'X-Naver-Client-Id':naver_id,
                'X-Naver-Client-Secret':naver_secret
            },
            files={
                'image':res.raw.read()
            })
        if clova_res.json().get('info').get('faceCount'):
            print(clova_res.json().get('faces'))
            text = "{}".format(clova_res.json().get('faces')[0].get('celebrity').get('value'))
        else:
            text = "인식된 사람이 없습니다."
    else:
    	# text 처리
    	text = doc['message']['text']
    	
    requests.get('{}/bot{}/sendMessage?chat_id={}&text={}'.format(base_url, token, chat_id, text))
    # requests.get(url)
    return '', 200

# 웹훅 설정(set webhook) == 텔레그램에게 알리미를 해달라고 하는 것
@app.route('/setwebhook')
def setwebhook():
    url = "{}/bot{}/setWebhook?url={}{}".format(base_url, token, my_url, token)
    res = requests.get(url)
    return '{}'.format(res), 200

# 텔레그램이 우리에게 알림을 줄 때 사용할 route
#   만약 특정 유저가 우리 봇으로 메시지를 보내면
#       텔레그램이 우리에게 알림을 보내온다.(json)

# @app.route('/')
# def index():
```

