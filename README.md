# kafka_message_example

그렇다. 회사에서 쓰길래 나도 써봄.

참고 : <https://coding-start.tistory.com/116?category=790331>

참고 : <https://semtax.tistory.com/83>


## 0. 환경

Windows 였다가 mac할래

RabbitMQ

-----

## 1. Rabbit MQ 다운받기

<https://www.rabbitmq.com/download.html>

여기서 다운 받으면 되는데 Erlang이 없으면 에러가 뜬다.
친절하게 에러가 뜨면 Erlang을 다운 받을 수 있는 사이트로 가서 다운 받게 해준다.

<https://www.erlang.org/downloads>

생각보다 오래 걸린다.

Window로 깔고나니

```cmd
rabbitmq-server 
```

에러남.  

맥북오면 할래..

-----

12.7
맥이 왔다.

환경 설치를 하고 시작.

### 구조

![](/img/Arch.png)



1. homebrew를 설치

-----

#### 에러발생

```text
Warning : /opt/homebrew/bin is not in your path
```

에러가 발생

##### 해결방법

vi ~/.zshrc 를 실행

```text
source ~/.bash_profile
export PATH="/opt/homebrew/bin:$PATH"
```

를 입력

-----

## 2. rabbitmq 설치

```text
brew install rabbitmq
```

```cmd
$rabbitmq-server <- 포그라운드 실행ß
$brew services start rabbitmq <- 백그라운드 실행
```

나 같은 경우 포그라운드 실행에서 

```textßß
zsh : command not found : rabbitmq-server
```

가 떠서 직접 경로로 들어가서 서버를 구동했다.

## 3. intellij 설치, 로제타 설치

어렵지 않다.

## 4. java 설치, maven, gradle 설치

```bash
brew tap adoptopenjdk/openjdk -> openjdk를 설치하기 위해 생성
brew install adoptopenjdk16 -> 16버전 설치
brew install maven
brew install gradle
```

## 5. Java 환경변수 설정

```bash
vi ~/.zshrc

export PATH=경로:$PATH
```

## 6. spring CLI 설치

```bash
brew tap spring-io/tap
brew install spring-boot
```

## 7. rabbitmq 사용

첫 번째 예제를 따라치긴 했는데, 이미 다른 예제에 적용된 것을 쓴거라 잘 되지 않음.

그리고 찾아보니 rabbitmq 보다 kafka가 빠르다는 것을 발견.


## 8. kafka 


```bash
brew install kafka
```

여기까지는 괜찮았으나, 

```bash
brew service kafka start 
```

에서 에러가 남.
service가 없단다.

```bash
brew commands
```
를 해보니 'service'란 명령어가 없음. 

보니까 저 예제 쓴 사람이 잘 못 씀

```bash
brew services start kafka
```

가 맞다.

무튼 kafka를 실행에 성공했으면

<https://victorydntmd.tistory.com/348>

이 블로그를 따라하면 된다.

docker로 올려서 local에서 확인하는 방법을 적겠다.

mac의 M1칩 기준 Kafka의 경로는 다음과 같다.

```bash
/opt/homebrew/Cellar/kafka/version/bin
```

여기서 관련 명령어들을 실행해주면 된다.

#### Error 발생 kafka Connect issue

zookeeper를 같이 켜줘야 한다.
그래서 zookeeper를 다운받아서 실행시켰다.

그런데 postman으로 요청을 보내니 405 에러가뜸.

jetty관련 어쩌구 뜨길래 jetty도 다운 받아줬다.

그런데 jetty가 mac M1칩은 지원은 안 하길래 찾아봄.

crystal을 다운받으란다.

이거 안 되서 
```bash
brew install cask jetty
```

했다.

그런데 m1칩은 지원 안 함.ㅋㅋㅋ

build.gradle에서 의존성 추가를해봄.

```text
compileOnly 'org.eclipse.jetty:jetty-maven-plugin:9.4.38.v20210224'
```


이것도 안 되면 자러감. 출근해야함.

실패.


+++
이유 찾았다.

zookeeper가 기본적으로 8080포트를 쓰는데, 이 포트랑 아파치 포트가 겹쳐서 오류가난 것. 
혹시 몰라서 스프링 부트 톰캣 서버 포트를 8090으로 바꿧는데 잘 됨.

하지만 매 번 포트를 바꿔줄 수 없는 노릇이므로, zookeeper의 포트를 바꿔주기로함.

zookeeper의 설정 파일을 가보면(/opt/homebrew/Cellar/zookeeper/3.7.0_1/share/zookeeper/examples) zoo_sample.cfg


```bash
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/tmp/zookeeper
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

## Metrics Providers
#
# https://prometheus.io Metrics Exporter
#metricsProvider.className=org.apache.zookeeper.metrics.prometheus.PrometheusMetricsProvider
#metricsProvider.httpPort=7000
#metricsProvider.exportJvmInfo=true
~                                                                                                                  
~                                                                                                                  
~                                           
```
d
요로콤 되어있다. 클라이언트포트가 2181인데 왜 이딴 오류가 발생하는걸까?

대충 스택오버플로우
<https://stackoverflow.com/questions/59943241/zookeeper-admin-server-port>
에 가보니 admin port도 따로 설정해줘야 하나? 했음.

```bash
admin.serverPort=2182
```

혹시 클라이언트 포트랑 겹칠까봐 그냥 2182로 설정한 값을 추가했더니 톰캣이랑 충돌 안나고 잘 됨.

+++++

그냥 이래도 안 됨.

뭐가 문제일까 생각해봤는데, kafka 설치할 때 이미 zookeeper도 설치된거라고 생각.

이유는 kafka 폴더 안에 'zookeeper-server-start'가 있음.

그냥 homebrew로 다운 받은거 다 삭제하고 tar버전으로 다운받아서 서버킨지 안 꼬이고 됨.

열받는다.


제대로 실행되면 다음과 같이 뜬다.

왼쪽은 zookeeper , 오른쪽은 kafka
![](/img/kafka_zookeeper.png)  


이제 포 그라운드에서 실행하여 된 것을 확인하였으니, 다음 명령어를 통해 백그라운드에서 실해하게 바꾸자.

무조건 주키퍼 먼저 실행해야한다. 안 그럼 카프카가 주키퍼를 못 찾아서 삽질하게 된다.!!!


```bash
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties -> 주키퍼 실행
bin/kafka-server-start.sh -daemon config/server.properties -> 카프카 실행
```