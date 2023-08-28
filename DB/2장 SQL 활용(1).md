### 표준조인
- ANSI/ISO SQL의 조인 형태
: INNER JOIN, NATURAL JOIN, CROSS JOIN, OUTER JOIN

- NATURAL JOIN
: 같은 이름을 가진 칼럼 전체에 대한 등가 조인, USING 조건절이나 ON 조건절 사용 불가, 같은 데이터 유형 칼럼만 조인 가능, 앨리어스나 테이블명 사용 불가
SQL>> SELECT 칼럼s FROM 테이블1 NATURAL JOIN 테이블2;

- INNER JOIN
: 행에 동일한 값이 있는 칼럼 조인, JOIN의 디폴트 옵션, USING 조건절이나 ON 조건절 필수, CROSS JOIN이나 OUTER JOIN과 동시 사용 불가, 두 테이블에 동일 이름 칼럼이 있을 경우 SELECT절에 앨리어스 필수
SQL>> SELECT 칼럼s FROM 테이블1 A, 테이블2 B WHERE A.칼럼=B.칼럼;
SQL>> SELECT 칼럼s FROM 테이블1 A INNER JOIN 테이블2 B ON A.칼럼=B.칼럼; (ANSI/ISO 표준)

➕ USING 조건절: 같은 이름을 가진 칼럼 중 등가 조인 대상 칼럼 선택, SQL Server에서는 지원하지 않음, 조건절에 앨리어스나 테이블명 불가
SQL>> SELECT 칼럼s FROM 테이블1 A JOIN 테이블2 B USING (칼럼명);

➕ ON 조건절: 다른 이름을 가진 칼럼 간 조인 가능 (앨리어스나 테이블명 필수), 괄호는 의무사항 아님
SQL>> SELECT 칼럼s FROM 테이블1 A JOIN 테이블2 B ON (A.칼럼=B.칼럼);

- CROSS JOIN
: 가능한 모든 조합으로 조인
SQL>> SELECT 칼럼 FROM 테이블1, 테이블2; (조인 조건이 없을 때 발생 ↔ NATURAL JOIN은 명시해야 됨)

- OUTER JOIN
: 조인 조건에서 행에 동일한 값이 없는 칼럼 조인, USING 조건절이나 ON 조건절 필수
➡️ LEFT OUTER JOIN: 좌측 테이블 데이터 조회 후 우측 테이블 조인 대상 데이터 조회
SQL>> SELECT 칼럼s FROM 테이블1 A, 테이블2 B A.칼럼=B.칼럼(+);
SQL>> SELECT 칼럼s FROM 테이블1 A
	  LEFT OUTER JOIN 테이블2 B
	  ON (A.칼럼=B.칼럼);

➡️ RIGHT OUTER JOIN 오른쪽 결과가 더 긺
➡️ FULL OUTER JOIN: LEFT와 RIGHT OUTER JOIN 포함
SQL>> SELECT 칼럼s FROM 테이블1 A	
	  FULL OUTER JOIN 테이블2 B
	  ON (A.칼럼=B.칼럼);

### 집합 연산자
- 집합 연산자: 조인 없이 여러 테이블의 관련 데이터를 조회하는 연산자
- UNION: 합집합, 칼럼 수와 데이터 타입이 모두 동일한 테이블 간 연산만 가능
SQL>> SELECT 칼럼명 FROM 테이블명 A WHERE 조건절 UNION SELECT 테이블명 WHERE 조건절;

- UNION ALL: 중복된 행도 전부 출력하는 합집합, 정렬 안함 (↔ UNION은 정렬을 유발함), 집합 연산자에 속함
SQL>> SELECT 칼럼명 FROM 테이블명 A WHERE 조건절 UNION ALL SELECT 테이블명 WHERE 조건절;
- INTERSECT: 교집합
SQL>> SELECT 칼럼명 FROM 테이블명 A WHERE 조건절MS INTERSECT SELECT 테이블명 WHERE 조건절;
- MINUS, EXCEPT: 차집합
SQL>> SELECT 칼럼명 FROM 테이블명 A WHERE 조건절 MINUS SELECT 테이블명 WHERE 조건절;

### 계층형 질의와 셀프 조인
#### 계층형 질의(Hierarchical Query)
: 계층형 데이터를 조회하기 위해 사용함, Oracle에서 지원함
- 계층형 데이터: 엔터티를 순환관계 데이터 모델로 설계할 때 발생함
- CONNECT BY : 트리 형태의 구조로 쿼리 수행 (루트 노드부터 하위 노드의 쿼리를 실행함) 상사 이름과 사람 이름을 조인하여 상사 밑에 넣기
	- START WITH : 시작 조건 지정
	- CONNECT BY PRIOR : 조인 조건 지정
		- LEVEL : 검색 항목의 깊이, 최상위 계층의 레벨은 1
		- CONNECT_BY_ROOT : 최상위 계층 값 표시
		- CONNECT_BY_ISLEAF : 최하위 계층 값 표시
		- SYS_CONNECT_BY_PATH : 계층 구조의 전개 경로 표시
- CONNECT BY절의 루프 알고리즘 키워드
	- NOCYCLE : 순환구조의 발생지점까지만 전개
	- CONNECT_BY_ISCYCLE : 순환구조 발생지점 표시 (부모 노드와 자식 노드가 같을 때 1 아니면 0 출력)
- LPAD : 계층형 조회 결과를 명확히 하기 위해 사용 (LEVEL 값을 이용하여 결과 데이터 정렬)

#### SQL Server 계층형 질의
: CTE(Common Table Expression)로 재귀 호출

#### 셀프 조인: 한 테이블 내에서 두 칼럼이 연관 관계가 있는 경우, 앨리어스 필수

### 서브쿼리
- 서브쿼리: 하나의 SQL문 안의 SQL문

#### 동작 방식에 따른 분류
- 비연관 서브쿼리: 메인쿼리 칼럼을 가지고 있지 않는 서브쿼리, 메인쿼리에 값을 제공하기 위한 목적으로 주로 사용함
	- Access Subquery: 제공자 역할
	- Filter Subquery: 확인자 역할
	- Early Filter Subquery: 데이터 필터링 역할

- 연관 서브쿼리(Associative Subquery): 메인쿼리의 결과를 조건이 맞는지 확인하기 위한 목적으로 주로 사용함

#### 반환 데이터 형태에 따른 분류
- 단일 행 서브쿼리: 실행 결과가 1건 이하인 서브쿼리, 단일 행 비교 연산자와 함께 사용
- 다중 행 서브쿼리: 실행 결과가 여러 건인 서브쿼리, 다중 행 비교 연산자와 함께 사용

➕ 다중 행 비교 연산자
- IN : 서브쿼리의 결과 중 하나의 값이라도 동일하다는 조건
- ANY : 서브쿼리의 결과 중 하나의 값이라도 만족한다는 조건
- ALL : 서브쿼리의 모든 결과값을 만족한다는 조건
- EXISTS : 서브쿼리의 결과를 만족하는 값이 존재하는지 여부를 확인하는 조건, ‘WHERE EXISTS (SELECT ~)’ (항상 연관 서브쿼리로 사용)

- 다중 칼럼 서브쿼리: 실행 결과로 여러 칼럼 반환, 주로 메인쿼리의 조건과 비교하기 위해 사용 (비교하고자 하는 칼럼의 개수와 위치가 동일해야 함)

#### 스칼라 서브쿼리
: 값 하나를 반환하는 서브쿼리, SELECT절에 사용하는 서브쿼리

#### 뷰
: 가상의 테이블, FROM절에 사용하는 뷰는 인라인 뷰(Inline View)라고 함

➕ 장점
- 독립성: 테이블 구조 변경 자동 반영
- 편리성: 쿼리를 단순하게 작성할 수 있음, 자주 사용하는 SQL문의 형태를 뷰로 생성하여 사용할 수 있음
- 보안성: 뷰를 생성할 때 칼럼을 제외할 수 있음

#### WITH
: 서브쿼리를 이용하여 뷰로 사용할 수 있는 구문
SQL>> WITH 뷰명 AS (SELECT ~)
