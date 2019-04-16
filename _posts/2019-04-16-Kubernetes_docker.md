---
layout: post
title: 02.도커
---

# 02. Docker

## 도커 

- 도커는 컨테이너를 이미지 파일로 빌드하고 배포하여 어디서나 실행할 수 있도록 해주는 오픈소스이다.

#### 도커는 어떻게 동작할까?
- 도커는 클라이언트 (docker) 와 서버 (dockerd) 로 구성되어있다.
- 모든 명령은 클라이언트에서 REST 로 서버에 요청되어 서버에서 수행한다.
- 동작방식은 아래와 같다
    1) Dockerfile 을 작성
    2) Dockerfile 과 함께 도커 빌드 요청,도커 서버에서는 도커 이미지를 빌드하여 로컬 저장소에 저장
    3) 도커 push 명령을 받으면 도커 서버는 로컬의 도커 이미지를 도커 레지스트리에 올린다.
    4) 도커 run 명령을 배포할 도커 서버에 전송
    5) 도커 run 명령을 받은 도커 서버는 도커 레지스트리 이미지를 로컬 저장소로 다운
    6) 도커 이미지를 이용하여 컨테이너를 시작


#### 설치
(본인은 Window 사용, aws와 gcp도 사용함.)

- [docker toolbox install](https://docs.docker.com/toolbox/toolbox_install_windows/)
- 설치 후 도커터미널에 고래모양이 보인다면 성공적!
- [dockerhub](https://hub.docker.com/) 가입하기

#### Hello World 컨테이너 실행
```
$docker run busybox echo "Hello world"
```

위 명령어를 실행하면
1. 도커 허브 레지스트리에서 최신 이미지를 가져온다.
2. 이미지가 다운로드되면 명령을 실행한다.
3. echo 명령은 text를 STDOUT에 출력하고
4. 프로세스 종료 후 컨테이너가 중지된다.

#### Docker 다른 이미지 실행하기

추가적인 내용은 [도커](보강)참조

#### Node.js 앱 만들기


```
const http = require('http');
const os = require('os');

console.log("Kubia server starting..");

var handler = function(request, response){
    console.log("Received request from"+ reques.connection.remoteAddress);
    respose.writeHead(200);
    respose.end("You've hit"+os.hostname()+"\n")
};
var www = http.createServer(handler);
www.listen(8080);
```

- 포트 8080에서 HTTP 서버 시작
- 모든 요청에 HTTP 응답 상태 코드 200 OK
- HTTP 요청 핸들러는 클라이언트의 ip주소를 표준출력에 기록


#### 도커 파일 만들기
```
From node:7
ADD app.js /app.js
ENTRYPOINT ["node","app.js"]
```

- FROM : 시작점으로 사용하는 컨테이너 이미지 정의 
- ADD : 로컬 디렉터리의 파일을 이미지의 루트 디렉터리에 동일한 이름으로 추가
- ENTRYPOINT : 이미지 실행 시 수행되는 명령 정의

#### 도커 이미지 만들기

```
$docker build -t kubia
```

현재 디렉토리내용을 kubia 이미지에 빌드 요청






