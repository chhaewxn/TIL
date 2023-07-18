## 오류 5

```java
package efub.eday.edayback.domain.day.dday.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.EnumType;
import jakarta.persistence.Enumerated;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@NoArgsConstructor
@Getter
public class Subject {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name = "subject_id")
	private Long id;

	@Enumerated(value = EnumType.ORDINAL)
	@Column(name = "d_day", nullable = false)
	private Dday dday;

	@Column(nullable = false)
	private String headline;

	public int getDday() {
		return dday.getRemainingDays();
	}

	public String getHeadline() {
		return headline;
	}
}
```

Subject 엔티티 안에 dday가 있는데, 문의사항 Request Body에서 dday를 요청했을 때, headline에 null을 입력할 수 없다는 메세지가 떴고, headline에 null을 입력할 수 있도록 코드를 수정하였을 때, 생각해보니 dday와 headline은 연관관계가 있는 칼럼이므로 문제가 생김을 깨달았다 ..! 

## 해결 5

Request Body에서 dday를 입력하지 않고, subjectId를 받는 것으로 코드를 수정하였다! 

```java
public class QueryService {
	private final QueryRepository queryRepository;
	private final MemberService memberService;
	private final SubjectRepository subjectRepository;

	public Query addQuery(QueryRequestDto requestDto) {
		Member writer = memberService.findMemberById(requestDto.getMemberId());

		Subject subject = subjectRepository.findById(requestDto.getSubjectId())
			.orElseThrow(() -> new IllegalArgumentException("유효하지 않은 ID"));

		Query query = Query.builder()
			.content(requestDto.getQueryContent())
			.writer(writer)
			.subject(subject)
			.build();

		return queryRepository.save(query);
	}
}
```
여기서 subjectId를 사용하여 데이터베이스에서 Subject 객체를 조회하는 것으로 코드 수정하였고 원하는 결과를 얻었다 =)

![](https://velog.velcdn.com/images/chhaewxn/post/1710408c-9309-4604-9958-86e4bfc3a4fd/image.png)
![](https://velog.velcdn.com/images/chhaewxn/post/4d801e5a-acd5-4d15-8e21-e7a8a93ccec2/image.png)

https://github.com/EFUB-EDAY/EDAY-BACK/issues/3

이제 칭호 API만 남았다 .. ! 
