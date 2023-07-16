# SWS 오류3 - DB연결 문제

## 오류 3
```
Communication link failure

The last packet successfully received from the server was 0 milliseconds ago. The last packet sent successfully to the server was 0 milliseconds ago.

The last packet successfully received from the server was 1 milliseconds ago. The last packet sent successfully to the server was 1 milliseconds ago.
```
스프링부트 실행시 이런 오류가 계속 떴다.. 
 마지막 패킷은 성공적으로 서버로 전송되었지만, 드라이버가 서버로부터 패킷을 수신하지 못했다라는 뜻인데

이 오류의 원인으로 구글링과 챗지피티에게 물어본 결과 4가지 정도 나왔다.

(1) 데이터베이스 서버가 실행 중이지 않거나 동작하지 않음: 먼저 MySQL 데이터베이스 서버가 실행 중인지 확인해야 한다. 데이터베이스 서버가 중지되어 있는 경우 실행시켜야 한다.

=> 아래 해결에도 나와있지만 나는 이 경우였다. 아주 간단하고 기본적인 에러였는데 해결하기 위해 많은 시간이 걸렸다 🥲

(2) 데이터베이스 연결 정보 오류: application.properties 또는 application.yml 파일에 올바른 MySQL 데이터베이스 연결 정보를 설정하는지 확인하기! 특히, 데이터베이스 URL, 사용자 이름, 비밀번호 등이 정확한지 확인해야 한다. 비밀번호의 경우엔 최소 글자수, 영문자, 특수문자 포함 등이 있기에 신경써서 작성해야한다. 1234 이런거 작성하면 안되었음

(3) 데이터베이스 드라이버 문제: MySQL 데이터베이스 드라이버가 올바르게 구성되었는지 확인하기! 종종 의존성 라이브러리 버전이 호환되지 않아서 발생할 수 있다. 

(4) 방화벽 문제: 데이터베이스 서버와 애플리케이션 사이의 네트워크 연결이 잘 설정되어 있는지, 방화벽 등에서 해당 포트가 차단되지 않았는지 확인해야 한다. 

## 해결 3
JDBD연결하는 URL에 useSSL=false도 해보고, mysql에서 보안 권한을 바꿔보기도 했을 때 해결되지 않아서 리드님께 물어봤을 때 혹시 mysql이 중지된건지 제어판->서비스 들어가보라하셨다!

![](https://velog.velcdn.com/images/chhaewxn/post/839435f7-a175-4e1f-8091-10514bb80d8a/image.png)

지난 도커 연결로 포트를 중지해놨을 때 mysql을 함께 중지해놓은 상태여서 그랬었다 .. ㅎㅎ mysql을 다시 실행하니까 오류 해결되었다! 😅

산넘어산이다 .. ㅠㅠ 팀에 폐가 되지 않고 싶은데 잘할 수 있을까 ㅠ.ㅠ

