SWS 백리드님이 도커 초기 세팅을 해주셨고, 프로젝트에 적용하는 과정에서 다양한 오류를 만났다. 리드님이 만들어주신것을 적용하는 것만 하는 것에도 문제가 와장창 발생해서 슬펐다 🥲🥲

## 오류 2-1
![](https://velog.velcdn.com/images/chhaewxn/post/282cd5e9-8e44-4f55-a16a-9406eac13154/image.png)

ERROR [app 8/8] COPY build/libs/*.jar app.jar

이라는 오류가 발생했다.

## 해결 2-1

build/libs 디렉토리가 존재하지 않는다는 오류이다. 빌드를 안하고 도커를 작동시킨 것이다 ! 😅😅

./gradlew build  명령어를 통해 빌드를 하면 build/libs가 생기고 다시 docker compose up하면 된다.

## 오류 2-2
![](https://velog.velcdn.com/images/chhaewxn/post/07ce3040-816e-49bb-83fd-cf80dd523240/image.png)
흠 그렇게 위의 오류는 해결하고 다시 돌려보았을 때, 포트 문제가 생겼다.

에러메세지는 Error response from daemon: Ports are not available: exposing port TCP 0.0.0.0:3306 -> 0.0.0.0:0: listen tcp 0.0.0.0:3306: bind: Only one usage of each socket address (protocol/network address/port) is normally permitted. 이렇게 떴고,

해당 오류는 도커에서 포트 충돌이 발생한 경우이다.

## 해결 2-2
3306 포트를 다른 곳에서 이미 실행시키고 있기에 발생한 오류이다.
어디서 저 포트를 쓰고있는지 모르겠지만 지금 당장 필요하기에 어딘가 쓰이고 있는 3306포트 실행을 중지시키는 것이 내 목표! 

![](https://velog.velcdn.com/images/chhaewxn/post/799fb2ae-8f66-496d-8260-b96f42446ca3/image.png)
```shell
Get-Process -Id (Get-NetTCPConnection -LocalPort 3306).OwningProcess
```
위의 코드를 이용하여 3306 포트를 사용하는 프로세스를 찾고,
```shell
Stop-Process -Id [프로세스 ID]
```
위의 코드로 해당 프로세스를 중지시켜주면 된다!

시작부터 험난하다 큰일이다
