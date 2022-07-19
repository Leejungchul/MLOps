# 쿠버네티스(Kubernetes)
- Linux 컨테이너 작업을 자동화하는 오픈소스 플랫폼
- 컨테이너를 포드(pod)로 분류하여 컨테이너 급증과 관련된 문제를 해결함
- > 포드(pod) : 그룹화된 컨테이너에 추상화 계층을 추가하여 사용자가 워크로드를 예약하고 네트워킹 및 저장소와 같은 필수 서비스를 컨테이너에 제공한다.


<br/>

- 관련 개념
> 마스터 : 쿠버네티스 노드를 제어하는 머신, 테스크 할당 시작
> 노드 : 할당된 테스크를 수행하는 시스템
> 포드 : 단일 노드에 배포된 하나 이상의 컨테이너 그룹, 포드 내의 컨테이너들은 ip, ipc, 호스트 이름 등을 공유하며 기본 컨테이너에서 네트워크와 스토리지 를 추상화함
> 복제 컨트롤러 : 클러스테에서 실행되어야 하는 동일한 포드 사본의 개수 제어
> 서비스 : 포드에서 작업 정의를 분리함
> kubectl : 쿠버네티스 명령줄 설정 툴 


## 미니쿠배(minikube)
- 쿠버네티스의 데모 앱 중 하나


- minikube를 위한 최소 사양
 > - cpu 2개 이상
 > - memory 2GB 이상
 > - Disk 20 GB 이상
![image](https://user-images.githubusercontent.com/86597163/178142536-dccd77d5-0af6-4a00-b5b3-6e1fd2a622f0.png)



#### 1) minikube 설치
```shell
curl -L0 https://storage.googleapis.com/minikube/releases/v1.22.0/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube --help  # 설치 확인
minikube version   # 버전 확인
```

#### 2) kubectl 설치
- kubectl : 쿠버네티스 클러스터(서버)에 요청을 간편하게 보내기 위한 클라이언트 툴

```shell
curl -L0 https://d1.k8s.io/release/v1.22.1/bin/linux/amd64/kibectl

sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl  # 권한과 위치 변경

kubectl --help  # 설치 확인
kubectl version  # 버전 확인
```

#### 3) minikube 실행
```shell
minikube start --driver=docker   # 도커 드라이버를 기반으로 시작
# 필요한 도커 이미지 다운, 이를 기반으로 minikube 실행

minikube status  # minikube 상태 확인

kubectl get pod -n kube-system  # default pod 생성 되었는지 확인

minikube delete  # 삭제하기
```

#### 4) Pod 생성
```shell
# Pod 예시
apiVersion: v1  # kubernetes resource 의 API version
kind: Pod  # kubernetes resource name
metadata:
    name: counter
spec:   
    container:
    - name: count  # 컨테이너 이름
    image: busybox  # 컨테이너 이미지
    args: [/bin/sh, -c, 'i=0; while true; do echo "$i: $(date)"; i=$((i+1)); sleep 1; done']
```
- 위 pod 스펙으로 pod 생성
```shell
vi pod.yaml

'''
pod 스펙 코드 입력
'''

esc
wq  # vi 저장 후 종료

kubectl apply -f pod.yaml  # pod.yaml을 적용한 쿠버네티스 리소스 생성 or 변경
```

```shell
kubectl get pod   # 포드 조회하기

# kubectl get pod pod-name  :  특정 포트 네임의 포트 조회

# kubectl describe pod <pod-name> : pod 자세히 조회

kubectl get pod -o wide   # pod 목록 자세히 출력

kubectl get pod <pod-name> -o yaml  # <pod-name>을 yaml형식으로 출력

kubectl get pod -w  # kubectl get pod 결과를 계속 보여주고 변화가 있을 때 업데이트 
```

#### 5) Pod 로그, 접속
```shell
kubectl logs <pod-name>  # 로그 확인
kubectl logs <pod-name> -f  # 로그를 계속 보여줌

# 포드 안에 컨테이너가 여러개 일 때
kubectl logs <pod-name> -c <container-name>
kubectl logs <pod-name> -c <container-name> -f


kubectl exec -it <pod-name> -- <명령어>  # 포드 내부 접속
kubectl exec -it <pod-name> -c <container-name> -- <명령어>
```

#### 6) Pod 삭제
```shell
kubectl delete pod <pod-name>
kubectl delete -f <yaml파일 경로>  # 리소스 생성할 때 사용한 yaml파일로도 삭제 가능


