## 오류 4
```
" Resolved [org.springframework.web.method.annotation.MethodArgumentTypeMismatchException: Failed to convert value of type 'java.lang.String' to required type 'efub.eday.edayback.domain.day.dday.entity.Dday'; Failed to convert from type [java.lang.String] to type [@org.springframework.web.bind.annotation.PathVariable efub.eday.edayback.domain.day.dday.entity.Dday] for value [1]] "
```
스프링부트에서 실행은 문제가 없었는데 포스트맨에서 작동을 확인하였을 때, 위와 같은 로그가 출력되면서 예상했던 Response가 나오지 않았다..😭 구글링과 챗지피티의 도움을 받아서 무엇이 오류인지 질문하였을 때, 
> "클라이언트가 요청을 보낼 때 경로 변수(@PathVariable)를 사용하여 Dday 열거형 타입의 값을 전달하는데, 해당 값을 변환할 수 없는 경우에 발생합니다. 예를 들어, 클라이언트가 /some-path/1과 같은 요청을 보낼 때 경로 변수 1을 Dday 열거형으로 변환할 수 없는 경우에 이 메시지가 발생합니다."

라는 답안을 받았고, Dday가 열거형으로 설정되어있어서 경로 변수에는 자동으로 문자열로 들어오기에 이를 열거형으로 변환하기 위한 추가작업이 필요함을 알았다.

## 해결 4 

```java
public static Dday fromRemainingDays(int remainingDays) {
        for (Dday dday : Dday.values()) {
            if (dday.getRemainingDays() == remainingDays) {
                return dday;
            }
        }
        throw new IllegalArgumentException("Invalid remaining days value: " + remainingDays);
    }
}
```
위와 같은 메소드를 통해 경로 변수로 받은 숫자를 Dday 열거형으로 변환하여 메소드에 전달할 수 있었다 ! 

그 결과 아래와 같이 포스트맨 테스트도 해결🥰
![](https://velog.velcdn.com/images/chhaewxn/post/3ff7036c-2907-4c95-ab67-a9bda8ceae75/image.png)


힘든 오류였다! 해결해서 기쁘지만 지금 또 문의사항 API 오류에 봉착하였다.. 화이팅 ,, 
