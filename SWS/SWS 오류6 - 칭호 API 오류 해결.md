## 오류 6
회원별로 마이페이지에 들어가서 칭호 페이지를 열었을 때, 아래와 같은 결과가 나와야하는데, 엔티티가 이미 존재하는데 이 엔티티를 연결하지 못하고 profile과 titleList의 칼럼을 하나의 DTO에 담으려다보니 기존의 member 엔티티, title 엔티티와 연결되지 않은 문제가 생겼다.
```json
{

	"profile" : 
			{ "nickname" : "채원",
		    "email" : "chaewon1019@naver.com",
		    "profileImage" : "http://eday.com/profile/imageurl/1.jpg",
				"level" : 1,
				"createdDate" : "2023-07-06T01:21:23.267346",
				"isActive" : true  },

	"titleList" : [
			{ "dday" : 7,
				"titleName" : "절대 셔틀버스를 놓치지 않는 이화인", 
				"titleImageUrl" : "http://eday.com/title/imageurl/1.jpg",
				"getTitle" : true  },
			{ "dday" : 6,
				"titleName" : "수강신청 첫날 올클한 이화인", 
				"titleImageUrl" : "http://eday.com/title/imageurl/2.jpg",
				"getTitle" : false  },
			{ "dday" : 5,
				"titleName" : "눈 감고도 보고서 잘 쓰는 이화인", 
				"titleImageUrl" : "http://eday.com/title/imageurl/3.jpg",
				"getTitle" : false  },
	    { "dday" : 4,
				"titleName" : "밥을 꼬박꼬박 먹고 다니는 이화인", 
				"titleImageUrl" : "http://eday.com/title/imageurl/4.jpg",
				"getTitle" : false  },
			{ "dday" : 3,
				"titleName" : "학문관 수선실의 뽀미 뽀삐와 노는 이화인", 
				"titleImageUrl" : "http://eday.com/title/imageurl/5.jpg",
				"getTitle" : false  },
			{ "dday" : 2,
				"titleName" : "대강당 계단 오르고도 숨 만차는 이화인", 
				"titleImageUrl" : "http://eday.com/title/imageurl/6.jpg",
				"getTitle" : false  },
			{ "dday" : 1,
				"titleName" : "이화그린의 피가 흐르는 어엿한 이화인", 
				"titleImageUrl" : "http://eday.com/title/imageurl/7.jpg",
				"getTitle" : false  }],
}
```
## 해결 6
DTO을 각각 만들었다! 

🔻 회원 정보의 데이터를 담는 MemberProfileDto
```java
package efub.eday.edayback.domain.day.title.dto;

import java.time.LocalDateTime;

import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class MemberProfileDto {

	private String nickname;
	private String email;
	private String profileImage;
	private int level;
	private LocalDateTime createdDate;
	private boolean isActive;

	public MemberProfileDto(String nickname, String email, String profileImage, int level, LocalDateTime createdDate,
		boolean isActive) {
		this.nickname = nickname;
		this.email = email;
		this.profileImage = profileImage;
		this.level = level;
		this.createdDate = createdDate;
		this.isActive = isActive;
	}
}
```

🔻 칭호 정보의 데이터를 담는 TitleDto
```java
package efub.eday.edayback.domain.day.title.dto;

import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class TitleDto {

	private int dday;
	private String titleName;
	private String titleImageUrl;
	private boolean getTitle;

	public TitleDto(int dday, String titleName, String titleImageUrl, boolean getTitle) {
		this.dday = dday;
		this.titleName = titleName;
		this.titleImageUrl = titleImageUrl;
		this.getTitle = getTitle;
	}
}

```

🔻 프로필, 칭호 정보를 담는 TitleResponseDto
```java
package efub.eday.edayback.domain.day.title.dto;

import java.util.List;

import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
public class TitleResponseDto {

	private MemberProfileDto profile;
	private List<TitleDto> titleList;

	public TitleResponseDto(MemberProfileDto profile, List<TitleDto> titleList) {
		this.profile = profile;
		this.titleList = titleList;
	}
}

```

profile: MemberProfileDto 객체를 담는 필드. 이 필드는 회원의 프로필 정보를 담고 있다!
titleList: TitleDto 객체들의 리스트를 담는 필드. 이 필드는 회원이 가지고 있는 칭호들에 대한 정보를 담고 있다! 

### Postman 실습 결과
![](https://velog.velcdn.com/images/chhaewxn/post/5c6b4137-b065-4226-9fc2-beb7dec9233a/image.png)
![](https://velog.velcdn.com/images/chhaewxn/post/49ffb9ea-7de7-4b7b-92d9-e846e9cc08ef/image.png)
