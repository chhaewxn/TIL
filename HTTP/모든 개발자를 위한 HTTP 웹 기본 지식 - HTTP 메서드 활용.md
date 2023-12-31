# 📝 HTTP 메서드 활용
## 클라이언트에서 서버로 데이터 전송

### 데이터 전달 방식
1) 쿼리 파라미터를 통한 데이터 전송
: GET에서 많이 사용하고 정렬필터나 검색기능에서 많이 사용한다.

2) 메세지 바디를 통한 데이터 전송
: POST, PUT, PATCH등 회원 가입, 수정, 상품 주문과 같이 리소스를 등록 및 수정하는데 사용한다.

## HTTP API 설계 예시

### 컬렉션
- POST 기반 등록으로 리소스 등록의 주체를 서버에서 담당한다.
- 회원관리 API같은 예시가 대표적이다. 
- 회원 등록시 회원의 식별 코드는 서버측에서 생성및 관리한다.

### 스토어
- PUT 기반으로 클라이언트가 자원의 URI를 알고 관리의 주체가 된다.
- 파일 등록 시스템 API같은 경우가 대표적이다.
- 파일 등록시 파일 이름은 클라이언트가 알고 있다.

### 문서
- 단일 개념으로 파일하나, 객체 인스턴스, 데이터베이스의 ROW등.

### 컨트롤러, 컨트롤 URI
- 문서, 컬렉션, 스토어 등으로 해결하기 어려운 추가 프로세스 실행에 사용된다.
- 동사를 사용하며 리소스의 행위를 사용한다. 
- HTML FORM 기반 설계의 경우 GET, POST만 사용할 수 있기에 PUT, PATCH, DELETE와 같은 행위에 대해서는 컨트롤 URI로 설계한다.
	- 회원 수정 → /members/{id}/edit	
	- 회원 삭제 → /members/{id}/delete
![](https://velog.velcdn.com/images/chhaewxn/post/7eac7a1f-2015-444a-9d2e-8caef02364c9/image.png)
