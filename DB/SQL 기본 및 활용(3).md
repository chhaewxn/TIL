# 📝 SQL 기본 및 활용(3)
## 함수
### 단일 행 함수
: SELECT절, WHERE절, ORDER BY절에 사용 가능, 각 행에 개별적으로 작용, 여러 인자를 입력해도 단 하나의 결과만 출력

#### 문자형 함수: 문자열 입력 시 문자열이나 숫자 반환
- LOWER, UPPER, LENGTH
- CONCAT : 문자열 결합
- SUBSTR : 문자열 부분 추출
- LTRIM, RTRIM, TRIM : 왼쪽 공백 제거, 오른쪽 공백 제거, 양쪽 공백 제거
- ASCII : 아스키 코드값 출력

#### 숫자형 함수
- ABS, SIGN : 절대값, 부호 (1, 0, -1 중 출력)
- MOD : 나머지, 연산자 ’%’로 대체 가능함
- ROUND, CEIL, FLOOR : 반올림, 올림, 버림 (‘함수(E,N)’으로 소수점 이후 N번째 자리까지 출력)
- TRUNC : 숫자형 부분 추출

#### 날짜형 함수
- SYSDATE : 현재 시각 출력 (년, 월, 일, 시, 분, 초)
- EXTRACT : 날짜형 부분 추출		SQL>> SELECT EXTRACT(부분 FROM SYSDATE) FROM DUAL;
- ±숫자, ±숫자/24 : 일자 연산, 시간 연산
- NEXT_DAY : 지정된 요일 첫 날짜 출력

#### 변환형 함수: 데이터 타입 변환, 명시적 형 변환 방식
- TO_NUMBER, TO_CHAR, TO_DATE (Oracle): 문자열을 숫자로, 숫자나 날짜를 문자열로, 문자열을 날짜로
- CAST, CONVERT (SQL Server)

#### NULL 관련 함수
- NVL(칼럼,값) : NULL 값 변환
- NVL2(칼럼,값,값) : NULL이면 앞의 값 아니면 뒤의 값 출력
- NULLIF(값,값) : 같으면 NULL 다르면 첫 값 출력
- COALESCE(값,값,…) : NULL이 아닌 첫 값 출력
- ISNULL(칼럼,값) : NULL이면 값으로 대치 아니면 칼럼 값 출력

### 데이터 변환
- 명시적 형 변환: 변환형 함수를 이용하여 데이터 타입 변환
- 암시적 형 변환: DBMS가 자동으로 데이터 타입 변환

### 조건문: IF-THEN-ELSE 형태
- CASE WHEN 조건절1 THEN 출력값1 … ELSE 기본값 END : ELSE 생략 시 NULL 출력
📌 ‘CASE WHEN NULL THEN 출력값 ELSE 기본값’은 조건이 없으므로 모든 행에서 기본값 출력 (일반적으로 ‘WHEN 칼럼 IS NULL’로 수정 필요)

- DECODE (칼럼, 기준값1, 출력값1, …, 기본값) : Oracle 함수, 기준값n이면 출력값n 출력

## GROUP BY, HAVING절
### 집계 함수(Aggregate Function)
: 그룹별 결과 출력, 다중 행 함수 중 하나, GROUP BY절이 없으면 그룹핑 대상이 존재하지 않아 에러 발생, WHERE절에 사용 불가, 공집합에서도 연산 수행

- ALL, DISTINCT : 전체 출력, 중복 제외 출력
- SUM, AVG, MAX, MIN, VARIAN, STDDEV : NULL 제외하고 연산 (↔ 숫자 연산은 NULL 출력)
- COUNT : 행 수 출력
	- COUNT(*) : NULL 포함
	- COUNT(표현식) : NULL 제외

### GROUP BY
: 그룹핑 기준 설정, 앨리어스 사용 불가

### HAVING
: GROUP BY절에 의한 집계 데이터에 출력 조건을 걺 
(↔ WHERE절은 SELECT절에 조건을 걸기 때문에 제외된 데이터가 GROUP BY 대상이 아님)
: 일반적으로 GROUP BY 뒤에 위치함

## ORDER BY절
### ORDER BY
: 특정 칼럼을 기준으로 정렬, 기본 정렬기준은 오름차순
- 1) 칼럼명 2) 앨리어스 3) 칼럼의 SELECT절에서 순서로 칼럼 지정 가능, SELECT절에 없는 칼럼도 지정 가능, GROUP BY절이 있으면 GROUP BY 대상 칼럼만 지정 가능

- Oracle은 NULL을 최대값으로 판단함 
(↔ SQL Server은 최소값으로 판단함)

### SELECT문 실행 순서
- 테이블에서 출력 대상이 아닌 것은 제거하고 그룹핑해서 그룹핑된 값이 조건에 맞는 데이터를 계산 및 출력하고 정렬함

SELECT 칼럼명 AS “별명” // ⑤ 계산 및 출력하고
FROM 테이블명 // ① 테이블에서
WHERE 조건식 // ② 출력 대상이 아닌 것은 제거하고
GROUP BY 칼럼/표현식 // ③ 그룹핑해서
HAVING 조건식 // ④ 그룹핑된 값이 조건에 맞는 데이터를
ORDER BY 칼럼/표현식 // ⑥ 정렬함

## 조인
### 조인
: 여러 테이블을 연결 또는 결합하여 데이터를 출력하는 것, 일반적으로 PK나 FK의 연관성에 의해 성립

### 등가 조인
: 두 테이블의 칼럼 값이 정확히 일치하는 경우, 대부분 PK와 FK 관계를 기반으로 함
SQL>> SELECT 칼럼s FROM 테이블1 A, 테이블2 B WHERE A.칼럼명=B.칼럼명;
- SELECT 대상 칼럼이 두 테이블 모두에 있는 경우 앨리어스를 지정해야 함 (양쪽 앨리어스 모두 무관)

### 비등가 조인
: 두 테이블의 칼럼 값이 정확하게 일치하지 않는 경우, 부등호나 BETWEEN 연산자를 통해 조인
