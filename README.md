# kafka_message_example

그렇다. 회사에서 쓰길래 나도 써봄.

참고 : <https://coding-start.tistory.com/116?category=790331>


## 0. 환경

Windows 였다가 mac할래

RabbitMQ,

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
$rabbitmq-server <- 포그라운드 실행
$brew services start rabbitmq <- 백그라운드 실행
```

나 같은 경우 포그라운드 실행에서 

```text
zsh : command not found : rabbitmq-server
```

가 떴다.. 하 맥북어렵다.

