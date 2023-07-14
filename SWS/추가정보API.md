추가정보 API 개발 중에...


🔻InfoImage // 추가정보 이미지 엔티티 코드
```java
package efub.eday.edayback.domain.day.info.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@NoArgsConstructor
@Getter
public class InfoImage {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name = "info_image_id")
	private Long id;

	@Column(name = "image_url")
	private String url;

	@ManyToOne
	@JoinColumn(name = "info_id")
	private Info info;

	@Builder
	public InfoImage(Long id, String url, Info info) {
		this.id = id;
		this.url = url;
		this.info = info;
	}
}
```

🔻Info // 추가정보 엔티티 코드
```java
package efub.eday.edayback.domain.day.info.entity;

import java.util.List;

import efub.eday.edayback.domain.day.dday.entity.DDay;
import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.OneToMany;
import jakarta.persistence.OneToOne;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@NoArgsConstructor
@Getter
public class Info {

	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(name = "info_id")
	private Long id;

	@OneToOne(mappedBy = "info")
	@JoinColumn(name = "d_day_id")
	private DDay dDay;

	@OneToMany(mappedBy = "info")
	private List<InfoImage> infoImageList;

	@Builder
	public Info(Long id, DDay dDay, List<InfoImage> infoImageList) {
		this.id = id;
		this.dDay = dDay;
		this.infoImageList = infoImageList;
	}
}
```

🔻InfoDto // 추가정보 DTO 코드
```java
package efub.eday.edayback.domain.day.info.dto;

import java.util.ArrayList;
import java.util.List;

import efub.eday.edayback.domain.day.info.entity.Info;
import efub.eday.edayback.domain.day.info.entity.InfoImage;
import lombok.AccessLevel;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@NoArgsConstructor(access = AccessLevel.PROTECTED)
public class InfoDto {
	private Long infoId;
	private Long infoImageId;
	private int dDay;
	private List<ImageDto> imageList;

	public static InfoDto from(Info info) {
		InfoDto infoDto = new InfoDto();
		infoDto.setInfoId(info.getId());
		infoDto.setDDay(info.getDDay().getDDay());

		List<ImageDto> imageList = new ArrayList<>();
		for (InfoImage infoImage : info.getInfoImageList()) {
			ImageDto imageDto = new ImageDto();
			imageDto.setImageId(infoImage.getId());
			imageDto.setImageUrl(infoImage.getUrl());
			imageList.add(imageDto);
		}
		infoDto.setImageList(imageList);

		return infoDto;
	}
}
```

추가정보와 추가정보 이미지는
![](https://velog.velcdn.com/images/chhaewxn/post/b1bf7358-f601-4033-bb5f-786dddd8f834/image.png)
위와 같은 관계이다.

Info와 디데이는 일대일, Info와 InfoImage는 일대다 관계
InfoDto 클래스에서는 Info 엔티티의 정보를 전달하기 위해 DTO로 사용된다. InfoDto.from() 메서드에서는 Info 엔티티 필드 값을 가져와서 InfoDto 객체에 설정하고, InfoImage 엔티티의 리스트를 순회하며 ImageDto 객체를 생성하여 imageList에 추가한다. 
