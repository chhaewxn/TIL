## 오류 7
처음에 내가 작성한 코드는 RequestBody에 subjectId를 입력하여 Subject 테이블과 연결하도록 하였다.
![](https://velog.velcdn.com/images/chhaewxn/post/cb994109-d26c-4afb-926a-5068e86cfdab/image.png)

```java
Subject subject = subjectRepository.findById(requestDto.getSubjectId())
			.orElseThrow(() -> new IllegalArgumentException("유효하지 않은 ID"));
```
위의 코드로 기본 `SubjectRepository` 코드를 통해 findById 메소드를 이용하면 되었다.

하지만, 문의사항 request에서 필요한 내용은 
subjectId가 아닌, dday였다!

## 해결 7

우선, `QueryRequestDto`에 
```java
private int dday;
```
코드를 추가하고,

`QueryService` 코드에 
```java
Dday dday = Dday.fromRemainingDays(requestDto.getDday());

Subject subject = subjectRepository.findByDday(dday)
			.orElseThrow(() -> new IllegalArgumentException("해당하는 Subject가 없습니다. dday: " + dday.getRemainingDays()));
```
를 추가하여서 Dday 객체를 선언하고 requestDto로부터 Dday의 남은 일수를 가져와서 초기화시킨다.

그리고, Subject 객체를 선언하고 subjectRepository라는 객체를 통해 db에서 dday와 일치하는 Subject를 조회한다.

`SubjectRepository` 코드에는 
```java
public interface SubjectRepository extends JpaRepository<Subject, Integer> {

	Optional<Subject> findByDday(Dday dday);
}
```
위의 코드로 findByDday 메서드를 선언하여 인자로 Dday 객체를 받아서 이 객체를 사용해 db에서 Subject를 조회한다. 메서드이름 그대로 Dday 객체의 dday 필드와 연관된 Subject를 찾는 쿼리를 자동으로 생성하여 실행하도록 한다.

아래처럼, dday를 입력하면 db에 문의사항이 저장된다! 
![](https://velog.velcdn.com/images/chhaewxn/post/69a57e73-20b3-433f-afcd-7ad7d2f2e6a7/image.png)
