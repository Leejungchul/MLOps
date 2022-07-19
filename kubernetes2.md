# 쿠버네티스(kubernetes)
## 디플로이먼트(Deployment)
- Pod와 Replicaset에 대한 관리를 제공하는 단위
  > 관리 : self-healing, scaling, rollout(무중단 업데이트) 등
- Pod를 감싼 개념
- pod를 deployment에 배포하여 여러 개로 복제된 pod와 여러 버전의 pod를 안전하게 관리

#### 1) Deployment 생성
```yaml
apiVersion: apps/v1 # kubernetes resource 의 API Version
kind: Deployment # kubernetes resource name
metadata: # 메타데이터 : name, namespace, labels, annotations 등을 포함
  name: nginx-deployment
  labels:
    app: nginx
spec: # 메인 파트 : resource 의 desired state 를 명시
  replicas: 3 # 동일한 template 의 pod 을 3 개 복제본으로 생성합니다.
  selector:
    matchLabels:
      app: nginx
  template: # Pod 의 template 을 의미합니다.
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx # container 의 이름
        image: nginx:1.14.2 # container 의 image
        ports:
        - containerPort: 80 # container 의 내부 Port
```
- 위 스펙으로 Deployment 생성

```shell
vi deployment.yaml

'''
위 코드 입력
'''

esc
wq
```

#### 2) Deployment 조회
```shell
kubectl get deployment   # 디플로이먼트 상태 확인

kubectl describe pod <pod-name>   # 자세하게 확인
```

#### 3) Auto-Healing
- Deployment 자체적으로 기능 회복 기능

```shell
kubectl delete pod <pod-name>  # pod 삭제

kubectl get pod  # pod 삭제 후 동일한 pod 새로 생성된 거 확인
```

#### 4) Scaling
- replica 개수 변경
```shell
kubectl scale deployment/nginx-deployment --replicas=5

kubectl get deployment

kubectl get pod
```

#### 5) Deployment 삭제
```shell
kubectl delete deployment <deployment-name>
```



### 서비스(Service)
- 서비스 : 쿠버네티스에 배포한 애플리케이션(pod)을 외부에서 접근하기 쉽게 추상화한 리소스
- 고정된 ip로 pod에 접근할 수 없기 때문에 클러스터 내/외부에서 pod에 접근할 때 서비스를 통해 접근한다.
- 서비스는 고정된 ip를 갖는다.


#### 서비스 생성 
#### 1) Deployment 생성

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
<br/>

```shell
vi deployment.yaml  # 위 코드 복사, deployment 설정

kubectl apply -f deployment.yaml  # yaml코드 적용

kubectl get pod -o wide  # pod의 ip 확인, 이 ip로 통신을 시도하면 오류 발생

minikube ssh  # minikube로 접속

curl -X GET <pod-ip> -vvv
ping <pod-ip>   # minikube에 접속하면 통신 가능
```

#### 2) 서비스 생성

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-nginx
  labels:
    run: my-nginx
spec:
  type: NodePort # Service 의 Type 을 명시하는 부분입니다. 자세한 설명은 추후 말씀드리겠습니다.
  ports:
  - port: 80
    protocol: TCP
  selector: # 아래 label 을 가진 Pod 을 매핑하는 부분입니다.
    app: nginx 
```

```shell
vi service.yaml   # 위 yaml 코드 입력

kubectl apply -f service.yaml

kubectl get service  # port 80:<port> 출력  ->  <port> 번호 확인

curl -X GET $(minikube ip):<port>  # 서비스를 통하면 클러스터 외부에서도 통신 가능
```




