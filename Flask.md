# Flask로 server구현

### 1. 모델 준비
- 모델 Sample code
~~~python
import os
import pickle

from sklearn.datasets import load_iris
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score, classification_report
from sklearn.model_selection import train_test_split

# Random seed : 데이터를 얼마나 랜덤하게 섞을지 설정, 숫자는 크게 의미 없지만 숫자만큼만 섞음
RANDOM_SEED = 1234  

# STEP 1) data load
data = load_iris()

# STEP 2) data split
X = data['data']
y = data['target']

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3,
                                                    random_state=RANDOM_SEED)

# STEP 3) train model
model = RandomForestClassifier(n_estimators=300, random_state=RANDOM_SEED)
model.fit(X_train, y_train)

# STEP 4) evaluate model
print(f"Accuracy :  {accuracy_score(y_test, model.predict(X_test))}")
print(classification_report(y_test, model.predict(X_test)))

# STEP 5) save model to ./build/model.pkl
os.makedirs("./build", exist_ok=True)
pickle.dump(model, open('./build/model.pkl', 'wb'))
# model.pkl : 모델 파일 확장자가 pkl
# w : 쓰기 모드, b : binary

~~~

### 2. 모델 학습 및 저장
~~~python
cd flask-tutorial

# 파이썬 버전을 확인합니다.
python -V

# requirements 를 설치합니다.
pip install scikit-learn==1.0

# 위의 코드를 복사 후 붙여넣습니다.
vi train.py

# 위의 코드를 실행시킵니다.
python train.py

# build 디렉토리 생성된 거 확인
ls -al

cd build

# build 디렉토리 안에 model.pkl파일 존재 확인
ls -al 
~~~
  > model.pkl 파일이 있으니 service를 만들 수 있음
> 디렉토리는 flask-tutorial
### 3. Flask server 구현
~~~python
import pickle

import numpy as np
from flask import Flask, jsonify, request

# 지난 시간에 학습한 모델 파일을 불러옵니다.
model = pickle.load(open('./build/model.pkl', 'rb'))
# rb : read binary, 텍스트 파일이 아니기 때문에 binary로 읽는다.


# Flask Server 를 구현합니다.
app = Flask(__name__)


# POST /predict 라는 API 를 구현합니다. POST방식으로 호출하면 아래 함수가 출력됨
@app.route('/predict', methods=['POST'])
def make_predict():
    # API Request Body 를 python dictionary object 로 변환합니다.
    request_body = request.get_json(force=True)
    # json : 데이터 표현이 편리하여 업계에서 통용됨

    # request body 를 model 의 형식에 맞게 변환합니다.
    X_test = [request_body['sepal_length'], request_body['sepal_width'],
              request_body['petal_length'], request_body['petal_width']]
    X_test = np.array(X_test)
    X_test = X_test.reshape(1, -1)  

    # model 의 predict 함수를 호출하여, prediction 값을 구합니다.
    y_test = model.predict(X_test)

    # prediction 값을 json 화합니다.
    response_body = jsonify(result=y_test.tolist())

    # predict 결과를 담아 API Response Body 를 return 합니다.
    return response_body


if __name__ == '__main__':  # 생성자 이름, 프로그램이 실행될 때 항상 나옴
    app.run(port=5000, debug=True)
~~~

~~~ shell
# vi에 위 소스코드 입력
vi flask_server.py 

# flask_server 실행
python flask_server.py
~~~

> 출력된 내용에서 flask server의 주소를 얻을 수 있다.
~~~shell
# 값을 json형태로 주면 바로 결과를 리턴
curl -X POST -H "Content-Type:application/json" --data '{"sepal_length": 5.9, "sepal_width": 3.0, "petal_length": 5.1, "petal_width": 1.8}' http://localhost:5000/predict
~~~
