SWS 개발이 거의 마무리 되어가는 시점에, 리드님이 스웨거를 미리 익혀두면 좋다고 말해주셔서 이번 포스팅은 스웨거에 대해 알아보도록 하자!

## 📝 스웨거(Swagger)란?

스웨거(Swagger)는 API 문서를 자동화하고 시각화하는 오픈 소스 프레임워크이다. 주로 RESTful API를 문서화하는데 사용되며, 개발자들이 API를 더 쉽게 이해하고 사용할 수 있도록 도와준다.

스웨거를 사용하면 API의 정확한 엔드포인트(Endpoint)와 매개변수, 요청 및 응답 형식, 인증 방법 등을 문서화하고 시각적으로 보여줄 수 있다. 이를 통해 개발자들은 API의 사용법을 쉽게 파악하고, 필요한 정보를 얻을 수 있으며, API를 더 효과적으로 개발 및 테스트할 수 있다!

## 스웨거 사용법

### 의존성 추가
build.gradle에 swagger 관련 dependency를 추가해준다.
```java
implementation "io.springfox:springfox-boot-starter:3.0.0"
implementation "io.springfox:springfox-swagger-ui:3.0.0"
```

**+) 현재는 springfox가 중단되어서 springdocs를 이용해야한다!!**
```java
// Swagger
implementation 'org.springdoc:springdoc-openapi-ui:1.6.6'
implementation 'org.jetbrains:annotations:20.1.0'
```

### SwaggerConfig.java 작성
```java
package efub.eday.edayback.config;

@Configuration
@EnableSwagger2
public class SwaggerConfig {

	@Bean
	public Docket api() {
		return new Docket(DocumentationType.SWAGGER_2)
			.select()
			.apis(RequestHandlerSelectors.basePackage("com.example.api")) // API 컨트롤러가 있는 패키지 지정
			.paths(PathSelectors.any())
			.build()
			.apiInfo(apiInfo());
	}

	private ApiInfo apiInfo() {
		return new ApiInfoBuilder()
			.title("Eday API 문서") // API 문서 제목
			.description("Eday API 문서에 대한 설명") // API 문서에 대한 간단한 설명
			.version("1.0.0") // API 버전
			.contact(new Contact("API 제공자 이름", "API 제공자 웹사이트", "API 제공자 이메일"))
			.build();
	}
}

```

### 스웨거 UI 확인
```
http://localhost:포트번호/swagger-ui.html
```
프로젝트를 실행하고 브라우저에서 다음 URL을 열면 스웨거 UI를 확인할 수 있다!
