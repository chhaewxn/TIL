### 그룹 함수
#### ANSI/ISO 표준 데이터 분석 함수
: 집계 함수, 그룹 함수, 윈도우 함수

#### 그룹 함수(Group Function)
: 합계 계산 함수, NULL을 빼고 집계함 (~ 집계 함수), 결과값 없는 행은 출력 안함
- ROLLUP : GROUP BY로 묶인 칼럼의 소계 계산, 계층 구조로 GROUP BY의 칼럼 순서가 바뀌면 결과 값 바뀜
- CUBE : 조합 가능한 모든 값에 대해 다차원 집계
- GROUPING SETS : 특정 항목에 대한 소계 계산, GROUP BY의 칼럼 순서와 무관하게 개별적으로 처리함

#### GROUPING : 그룹 함수에서 생성되는 합계를 구분해주는 함수, 소계나 합계가 계산되면 1 아니면 0 반환

### 윈도우 함수
#### 윈도우 함수(Window Function): 여러 행 간의 관계 정의 함수, 중첩 불가
- 순위 함수
	- RANK : 중복 순위 포함
	- DENSE_RANK : 중복 순위 무시 (중간 순위를 비우지 않음)
	- ROW_NUMBER : 단순히 행 번호 표시, 값에 무관하게 고유한 순위 부여

- 일반집계 함수: SUM, MAX, MIN, AVG, COUNT
- 행 순서 함수
	- FIRST_VALUE, LAST_VALUE : 첫 값, 끝 값
	- LAG, LEAD : 이전 행, 이후 행 (Oracle) 랙릿
	- ‘LEAD(E,A)’는 E에서 A번째 행의 값을 호출하는 형태로도 쓰임 (A의 기본값은 1)

- 비율 관련 함수
	- PERCENT_RANK() : 백분율 순서
	- CUME_DIST() : 현재 행 이하 값을 포함한 누적 백분율
	- NTILE(A) : 전체 데이터 A등분
	- RATIO_TO_REPORT : 총합계에 대한 값의 백분율

#### 윈도우 함수 문법
SQL>> SELECT 윈도우함수(A) OVER (PARTITION BY 칼럼 ORDER BY 칼럼 윈도잉절) FROM 테이블명;
- PARTITION BY : 그룹핑 기준
- ORDER BY : 순위 지정 기준
- 윈도잉절 : 함수의 대상이 되는 행 범위 지정
	- BETWEEN A AND B : 구간 지정
	- N PRECEDING, N FOLLOWING : N번째 앞 행, N번째 뒤 행
	- UNBOUNDED PRECEDING, UNBOUNDED FOLLOWING : 첫 행, 끝 행
	- CURRENT ROW : 현재 행
	- ROWS, RANGE : 행 지정, 값의 범위 지정

### DCL
#### DCL
: 유저를 생성하거나 권한을 제어하는 명령어, 보안을 위해 필요함

- GRANT: 권한 부여
SQL>> GRANT 권한 ON 오브젝트 TO 유저명;

- REVOKE: 권한 제거
SQL>> REVOKE 권한 ON 오브젝트 TO 유저명;

#### 권한(Privileges)
- SELECT, INSERT, UPDATE, DELETE, ALTER, ALL : DML 관련 권한
- REFERENCES : 지정된 테이블을 참조하는 제약조건을 생성하는 권한
- INDEX : 지정된 테이블에서 인덱스를 생성하는 권한

#### Oracle의 유저
- SCOTT: 테스트용 샘플 유저
- SYS: DBA 권한이 부여된 최상위 유저
- SYSTEM: DB의 모든 시스템 권한이 부여된 DBA

#### ROLE
: 권한의 집합, 권한을 일일이 부여하지 않고 ROLE로 편리하게 여러 권한을 부여할 수 있음

### 절차형 SQL
#### 절차형 SQL
: 일반적인 개발언어처럼 절차지향적인 프로그램을 작성할 수 있도록 제공하는 기능

- SQL문의 연속적인 실행 및 조건에 따른 분기처리를 이용하여 특정 기능을 수행하는 저장 모듈 생성 가능
- PL/SQL (Oracle)
- 블록 구조: 블록 내에 1) DML 2) 쿼리 3) IF나 LOOP 등을 사용할 수 있음
	- Declare(선언부): 블록에서 사용할 변수나 인수에 대한 정의
	- Begin(실행부): 처리할 SQL문 정의
	- Exception(예외 처리부): 블록에서 발생한 에러 처리 로직 정의, 유일한 선택 항목
	- T-SQL (SQL Server)

#### 프로시저(Procedure)
#### 사용자 정의 함수
: 절차형 SQL을 로직과 함께 DB 내에 저장해 놓은 명령문 집합, RETURN을 통해 반드시 하나의 값 반환 (↔ 프로시저)

#### 트리거(Trigger)
: DML문이 수행되었을 때 자동으로 동작하는 프로그램 (↔ 프로시저는 EXECUTE로 실행함), DCL와 TCL 실행 불가 (↔ 프로시저는 사용 가능함)
