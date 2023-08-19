# 📝 과목2 SQL 기본 및 활용 문제풀이

## p.60 - 41번
![](https://velog.velcdn.com/images/chhaewxn/post/ec049aac-4387-4e83-8f09-c2e1fd9a7edc/image.png)

해설: 라인수를 구하기 위해서 함수를 이용하여 작성한 SQL

- LENGTH : 문자열의 길이를 반환하는 함수
- CHR : 주어진 ASCII 코드에 대한 문자를 반환하는 함수
(CHR(10) : 줄바꿈)
- REPLACE : 문자열을 치환하는 함수
(REPLACE(C1, CHR(10)) : 줄바꿈 제거 )

## p.63 - 45번
![](https://velog.velcdn.com/images/chhaewxn/post/deaf62a4-8b15-42b9-85fa-ca88d0f3affe/image.png)

해설 : ISNULL 함수는 결과값이 NULL일 경우 지정된 값을 반환한다. 칼럼의 NULL 값을 확인할 때는 ISNULL을 사용해야 한다.

## p.71 - 58번
![](https://velog.velcdn.com/images/chhaewxn/post/60badc86-9a46-47dc-88c7-db00da969e7f/image.png)

해설 : CASE절을 이용해서 원래의 정렬 순서를 변경하였다. 그래서 ID가 'A'인 것이 가장 먼저 표시되도록 함 


### ➕ 보충 해설 
**DDL(데이터 정의어)** : CREATE ALTER DROP TRUNCATE RENAME
**DML(데이터 조작어)** : SELECT INSERT UPDATE DELETE
- 비절차적 데이터 조작어는 사용자가 무슨(WHAT) 데이터를 원하는지
- 절차적 데이터 조작어는 사용자가 어떻게(HOW) 데이터를 접근해야 하는지

**DCL(데이터 제어어)** : GRANT REVOKE
**TCL(트랜잭션 제어어)** : COMMIT ROLLBACK CHECKPOINT

### NULL
- 아스키코드 0번이며, 공백(32번)이나 0(48번)과는 전혀 다른 값이며, 조건에 맞는 데이터가 없을 때의 공집합과도 다르다. 
- 아직 정의되지 않은 미지의 값이며 현재 데이터를 입력하지 못하는 경우를 의미함
- NULL과 산술연산을 수행한 경우 그 결과는 항상 NULL

### 제약조건
- UNIQUE는 테이블 내에 중복되는 값이 없지만 NULL 입력 가능함
- PK는 주키로 테이블당 1개만 생성 가능
- FK는 외래키로 테이블당 여러 개 생성 가능
- NOT NULL은 명시적으로 NULL 입력을 방지

