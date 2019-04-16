---
layout: post
title: 03. 쿠버네티스?
---

# 03. Pod

## 포드(Pod)

포드라고도 하고 파드라고도 하는 것 같은데, 포드는 간단하게 말하면 컨테이너 그룹이다.

**특징**
- 컨테이너의 공동의 배치된 그룹.
- 쿠버네티스의 기본 빌딩 블록.
- 컨테이너 개별 배포 대신 컨테이너의 포드를 배포하고 운영한다.
- 일반적으로 포드는 전부 단일의 워커노드에서 실행된다.

#### 왜 필요할까?

컨테이너의 프로세스는 그 프로세스가 하위의 프로세스를 만들지 않는 한 한 컨테이너가 한 프로세스만 실행한다. 충돌이 났을 경우에도 개별 프로세스를 자동실행되게 하여야 한다.

이 경우에 여러개의 프로세스를 하나의 컨테이너로 묶게 되면 위와 같이 하나의 개별 프로세스를 자동실행되게 하는 거은 불가능하다. 그래서 상위 레벨의 구조가 필요하며 포드는 연관성이 있는 프로세스를 함께 실행하고 하나의 컨테이너에서 실행하는 것처럼 해줄 수 있다.

#### 포드 확인
- 포드 확인 (목록 조회, 변경 상태)
  - pending (보류)
  - Running (실행)
```
$kubectl get pods
```


#### 포드 사용법

- 컨테이너가 동일한 IP 및 Port 공간 공유하기
  - 네임스페이스도 동일한 ip를 공유와 port 공간을 공유한다. **동일 포드의 컨테이너에서 실행 중인 프로세스는 동일한 포트번호에 바인딩 되지 않게 주의 필수** 포트충돌에 주의해야한다.
- 클러스터의 모든 포드는 한개의 플랫, 공유스페이스, 네트워크 어드레스 스페이스에 위치한다. 그러니깐 다른 포드에 액세스가 가능하다는 말이다. *포드사이에는 NAT* 게이트웨이가 없다.

- 개별 포드는 특정 애플리케이션만 호스팅
  - 포드는 가벼워서 오버헤드가 거의 없다 여러개의 포드를 하나의 포드로 구성하자
- 멀티포드사용하기 
  - 백/프론트를 단일 포드에 구성하는 것은 좋은 방법은 아니다
- 각각 스케일링 가능한 포드로 분할
  - 쿠버네티스는 개별 컨테이너를 수평으로 확장할 수 없으므로 전체 포드 크기를 조정해야한다.
- 하나의 포드 여러개의 컨테이너? 애플리케이션은 주요프로세스+보조프로세스로 구성되기때문에 어떻게 구성해야할지 항상 생각해야한다.

#### YAML , JSON 파일 디스크립터에서 포드 만들기

  - 포드 전체의 YAML 정의가져오기
```
kubectl get po kubia-zxzij -o yaml
```

- 포드정의
  - 메타데이터(Metadata) : 포드관련이름, 네임스페이스. 라벨 등의 정보가 들어있음.
  - 스펙(Spec) : 포드의 컨테이너, 볼륨, 그 외의 데이터와 같은 포드 내용의 실제 설명이 들어있음.
  - 상태(Status) : 포드상태, 각 컨테이너 설명/상태, 포드 내부 ip 및 그 밖의 기본 정보등과 실행중인 포드의 현재 정보가 들어있다.

#### 디스크립터 만들기

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual
spec:
  containers:
  - image: luksa/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
```
#### 포드만들기

- kubectl create -f : 포드 및 다른 리소스 생성
```
$kubectl create -f kubia-manual.yaml
```

- 실행 중 포드 전체의 정의검색
```
$kubectl get po kubia-manual -o yaml
```
- json 반환요청
```
$kubectl get po kubia-manual -o json
```
- 새로 생성된 포드 보기
```
$kubectl get pods
```

#### 로그 확인
- 컨테이너 로그가져오기
```
$docker logs <container id>
```
- 포드 로그 가져오기
```
$kubectl logs kubia-manual
```
- 다중 컨테이너 포드 로그 가져오기
```
$kubectl logs kubia-manual -c <컨테이너 이름>
```

#### 요청 보내기
- 포워딩 ( 로컬 8888 : pod 8080 으로) : 특정포드와 통신하고 싶을 때 포트 포워딩 사용
```
kubectl port-forward kubia-manual 8888:8080
```

- 포드 전달자로 포드 연결 : 다른 터미널에서 실행되는 kubectl 포트 전달 프록시 통해 HTTP요청.
```
$curl localhost:8888
```

## 라벨

포드의 상위개념느낌으로 이해가 됨. (상위집합)

- 리소스에 첨부하는 임의의 키/값 쌍

#### 포드 만들 때 라벨 지정하기

```
apiVersion: v1
kind: Pod
metadata:
  name: kubia-manual-v2
  labels:
    creation_method: manual
    env: prod
spec:
  containers:
  - image: luksa/kubia
    name: kubia
    ports:
    - containerPort: 8080
      protocol: TCP
```
- 라벨 보기
```
$kubectl get po --show-labels
```
- 특정라벨보기
```
kubectl get po -L 라벨
```
- 라벨수정
```
```





*참고자료*
- kubenetes in action
- [google cloud study jam (quick lab) : Orchestrating the Cloud with Kubernetes](https://www.qwiklabs.com/focuses/557?parent=catalog)
- docker 문서
- kubernetes.io