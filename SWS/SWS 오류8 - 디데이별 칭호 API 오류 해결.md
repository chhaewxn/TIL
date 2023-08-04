## 문제 상황
원래 칭호 API 구현은 
아래와 같은 Response가 나오도록 코드를 작성하였다.
```json
{
  "profile": {
    "nickname": "송채원",
    "profileImage": "http://k.kakaocdn.net/dn/dCXFZ1/btslb57PHBP/BG8A7hJZ4o/img_640x640.jpg",
    "level": 1,
    "createdDate": [
      2023,
      8,
      4,
      7,
      45,
      57
    ],
    "active": true
  },
  "titleList": [
    {
      "dday": 7,
      "titleName": "절대 셔틀버스를 놓치지 않는 이화인",
      "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/D-7.png",
      "getTitle": false
    },
    {
      "dday": 6,
      "titleName": "수강신청 첫날 올클할 이화인",
      "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/D-6.png",
      "getTitle": false
    },
    {
      "dday": 5,
      "titleName": "눈 감고도 보고서 잘 쓰는 이화인",
      "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/D-5.png",
      "getTitle": false
    },
    {
      "dday": 4,
      "titleName": "밥을 꼬박꼬박 먹고 다니는 이화인",
      "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/D-4.png",
      "getTitle": false
    },
    {
      "dday": 3,
      "titleName": "학문관 수선실의 뽀미 뽀삐와 노는 이화인",
      "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/D-3.png",
      "getTitle": false
    },
    {
      "dday": 2,
      "titleName": "대강당 계단 오르고도 숨 안차는 이화인",
      "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/D-2.png",
      "getTitle": false
    },
    {
      "dday": 1,
      "titleName": "이화그린의 피가 흐르는 어엿한 이화인",
      "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/D-1.png",
      "getTitle": false
    }
  ]
}
```
위의 코드는 마이페이지에서 프로필 정보와 획득한 칭호를 한번에 볼 때 사용되는 용도의 API였고, 프론트엔드와 연결하는 과정에서 칭호를 눌렀을 때 칭호 하나씩 크게 보여지는 API가 추가로 필요하다고 말씀해주셔서 **특정 디데이에 해당하는 칭호 정보를 조회하는 API**를 추가 개발을 하였다. 

### TitleController
```java
@Operation(summary = "특정 디데이에 해당하는 칭호 정보 조회", description = "특정 디데이(d_day)에 해당하는 칭호 정보를 조회하는 메서드입니다.")
	@ApiResponse(responseCode = "200", description = "칭호 정보 조회 성공",
		content = @Content(mediaType = "application/json",
			schema = @Schema(implementation = TitleDto.class)))
	@GetMapping("/{d_day}")
	public ResponseEntity<TitleDto> getTitleByDday(@PathVariable("d_day") int dday) {
		Dday ddayEnum = Dday.fromRemainingDays(dday);
		TitleDto titleDto = titleService.getTitleByDday(ddayEnum);
		return ResponseEntity.ok(titleDto);
	}
```
추가정보 API에서 마주친 오류해결 덕분에, 그때보단 쉽게 코드를 작성할 수 있었다. 

dday를 int형으로 받아왔을 때, 열거형 dday로 바로 읽어오지 못해서, 문자열 값을 열거형으로 변환하는 메소드 fromRemainingDays()를 이용하여 작성하였다!😊

### TitleService
```java
@Transactional(readOnly = true)
	public TitleDto getTitleByDday(Dday dday) {
		Member member = authService.getCurrentMember();
		MemberTitle memberTitle = memberTitleRepository.findByMemberAndTitleSubjectDday(member, dday)
			.orElseThrow(() -> new ResponseStatusException(HttpStatus.NOT_FOUND, dday + ": 해당 디데이의 칭호를 찾을 수 없습니다."));

		Title title = memberTitle.getTitle();
		return new TitleDto(
			title.getSubject().getDday(),
			title.getName(),
			title.getImageUrl(),
			memberTitle.getGetTitle()
		);
	}
```

getTitleByDday 메소드를 이용하여 Dday 객체를 매개변수로 받고, TitleDto 객체를 반환하기! Title의 dday, name, imageurl정보와 MemberTitle의 getTitle 정보를 생성한 TitleDto 객체에 담아서 메서드의 결과로 반환되도록 하였다. 


### MemberTitleRepository
```java
@Repository
public interface MemberTitleRepository extends JpaRepository<MemberTitle, Integer> {
	Optional<MemberTitle> findByMemberAndTitleSubjectDday(Member member, Dday dday);
}
```
member와 dday 객체를 받아 해당하는 MemberTitle을 찾는 메소드도 추가!

## 결과
![](https://velog.velcdn.com/images/chhaewxn/post/0ff3f60f-39fa-4c9d-bc0a-8cd47218ff5e/image.png)
위와 같이 API 생성 완료!
https://api.ewha-day.com/title/5 요청하면 
```json
{
  "dday": 5,
  "titleName": "눈 감고도 보고서 잘 쓰는 이화인",
  "titleImageUrl": "https://eday-s3-bucket.s3.ap-northeast-2.amazonaws.com/title(%EC%B9%AD%ED%98%B8)/%EC%B9%AD%ED%98%B85.png",
  "getTitle": false
}
```

![](https://velog.velcdn.com/images/chhaewxn/post/775ad1e0-889d-490b-a76d-6fdba87a23ca/image.png)
이렇게 우리팀 디자이너님이 만들어준 아주 귀여운 이미지URL을 반환한다!



