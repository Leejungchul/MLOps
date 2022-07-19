# 도커(Docker)
## 도커란?
- 도커는 리눅스의 응용 프로그램들을 소프트웨어 컨테이너 안에 배치시키는 일을 자동화하는 오픈 소스 프로젝트이다.

- 독립적인 컨테이너가 하나의 리눅스 인스턴스 안에서 실행할 수 있게 한다.
- 서비스가 확장되어 많은 서버와 리소스를 가지고 운영해야 할 때 이를 관리하기 위한 컨테이너 기반의 오픈소스 가상화 플랫폼이다.
- OP서버, AWS, Azure, GCP클라우드 환경 등에서 실행할 수 있다.

<br/>


### 컨테이너(Container)
- 격리된 공간에서 프로세스가 동작하는 기술
- 리눅스 커널이 제공하는 기능 위에 빌드되어 별도의 운영 체제를 요구하거나 포함하지 않음
- 그러나 커널의 기능에 의존하며 리소스를 격리시킬 수 있음

> VM이나 VirtualBox 같은 가상머신은 호스트 OS위에 게스트 OS 전체를 가상화하여 사용하는 방식, 사용법이 간단하지만 무겁고 느림
- 이를 개선하기 위해 프로세스를 격리하는 방식의 컨테이너 등장


### 이미지(Image)
- 컨테이너 실행에 필요한 파일과 설정값 등을 포함한다.
- 컨테이너는 이미지를 실행한 상태이다.
- 같은 이미지에서 여러 컨테이서를 생성할 수 있고 컨테이너의 상태가 바뀌거나 삭제되어도 이미지는 변하지 않고 남는다.



### 도커 실습
#### **Docker 설치**
1) Set up the repository
  ```shell
  # apt라는 패키지 매니저 업데이트
  sudo apt-get update
  sudo apt-get upgrade
  ```
  ```shell
  # Docker의 패키지들 설치
  sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
  ```
  ```shell
  # Docker의 GPG key 추가 
  # *GPG : 암호화 방법의 하나
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
  ```
  ```shell
  # stable 버전의 repository를 바라보도록 설정
  echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  ```

  2) Install Docker Engine
  ```shell
  # Docker 엔진의 최신 버전 설치
  sudo apt-get update
  sudo apt-get install docker-ce docker-ce-cli containerd.io
  ```

  3) 정상 설치 확인
  ```shell
  # Docker 컨테이너를 실행하여 정상 설치 확인
  sudo docker run hello-world
  ```

<br/>


#### **Docker 권한 설정**
- 모든 docker 관련 작업이 root 유저에게만 권한이 있기 때문에 sudo를 붙여 명령 수행을 할 수 있다.
- root 유저가 아닌 host의 기본 유저에게도 권한을 주기위해 아래의 코드를 수행한다.
```shell
sudo usermod -a -G docker $USER
sudo service docker restart
```
코드 수행 후 재접속하면 권한 설정이 완료된다.

<br/>

#### **Docker의 기본적인 명령**
1) Docker pull
```shell
# Docker image repository 부터 Docker image를 가져오는 커멘드
# docker pull -help
doccker pull ubuntu:18.04 # ubuntu18.04버전 가져오기
```

2) Docker images
```shell
# 로컬에 존재하는 docker image 리스트를 출력하는 커멘드
# docker images --help
docker images
```

3) Docker ps
```shell
# 현재 실행중인 도커 컨테이너 리스트를 출력
# docker ps --help
docker ps
docker ps -a
```

4) Docker run
```shell
# 도커 컨테이너를 실행시키는 커멘드
# docker run --help
docker run -it --name demo1 ubuntu:18.04 /bin/bash
```
> -it : -i 옵션 + -t 옵션
> - 컨테이너를 실행시킴과 동시에 interactive한 터미널로 접속시켜줌

> --name : name
> - 컨테이너 id 대신 구분하기 쉽도록 지정해주는 이름

> /bin/bash
> - 컨테이너를 실행시킴과 동시에 실행할 커멘드, bin/bash 는 bash 터미널을 사용하는 것을 의미

5) Docker exec
```shell
# Docker 컨테이너 내부에서 명령을 내리거나, 내부로 접속하는 커멘드
# docker exec --help
docker run -it -d --name demo2 ubuntu:18.04   # -d : 백그라운드에서 실행, 컨테이너에 접속 종료해도 계속 실행되게 함
docker ps   # demo2 실행 중

docker exec -it demo2 /bin/bash  # 위 코드와 동일하게 컨테이너 내부에 접속할 수 있는 것을 확인 가능
```

6) Docker logs
```shell
# 도커 컨테이너의 log를 확인하는 커멘드
# docker logs --help
docker run --name demo3 -d busybox sh -c "while true; do $(echo date); sleep 1; done"  # busybox 이미지를 백그라운드에서 도커 컨테이너로 실행하여 1초에 한 번씩 현재 시간을 출력

docker logs demo3
docker logs demo3 -f  # -f : 계속 watch하며 출력
```

7) Docker stop
```shell
# 실행 중인 도커 컨테이너 중단시키는 커멘드
# docker stop --help
docker stop demo3
docker stop demo2
docker stop demo1
```

8) Docker rm
```shell
# 도커 컨테이너를 삭제하는 커멘드
# docker rm --help
docker rm demo3
docker rm demo2
docker rm demo1
```


9) Docker rmi
```shell
# 도커 이미지를 삭제하는 커멘드
# docker rmi --help
docker images
docker rmi ubuntu
```


#### **Docker 실습**
```shell
mkdir docker-practice  # mkdir : 디렉토리 만드는 명령, docker-practice라는 디렉토리 만들기

touch Dockerfile  # 빈 파일 생성 / ls 명령어로 생성된 파일 확인 가능

vi Dockerfile  # Dockerfile 수정
# i  insert 모드로 전환, 전환 후 아래 코드 입력

'''
FROM ubuntu:18.04

RUN apt-get update

CMD ["echo", "Hello Docker"]  # CMD : 명령 입력
'''
# esc
# :wq   저장 후 vi 나가기
```

도커 build
```shell
docker build -t my-image:v1.0.0.  # 도커 말기, -t : 실행, my-image라는 이름으로 만듦, 버전은 v1.0.0, . : 현재 디렉토리 / 현재 경로에 도커 파일이 있는지 확인하고 빌드 진행

docker images | grep my  # 이미지들 중 my가 포함된 이미지 가져오기

docker run my-image:v1.0.0   # 생성한 이미지 실행

docker run -d -p 5000:5000 --name registry registry
# -d : 데몬 모드, 컨테이너가 백그라운드로 실행
# -p: 호스트와 컨테이너의 포트 연결 <호스트 포트>:<컨테이너 포트>

docker tag my-image:v1.0.0 localhost:5000/my-image:v1.0.0  # localhost:5000에 올릴 my-image 복사

docker push localhost:5000/my-image:v1.0.0 # localhost:5000에 my-image 저장

curl -X GET http://localhost:5000/v2/_catalog
curl -X GET http://localhost:5000/v2/my_image/tags/list

docker tag my-image:v1.0.0 leejungchul/my-image:v1.0.0   
docker push leejungchul/my-image:v1.0.0   # 도커 허브로 푸시
```