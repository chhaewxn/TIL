## Select문
### 조인
- 두 개 이상의 릴레이션으로부터 연관된 투플들을 결합
- 조인 조건은 두 릴레이션 사이에 속하는 애트리뷰트 값들을 비교 연산자로 연결 ( = )
- 조인 조건을 생략했을 때와 틀리게 표현했을 때 카티션 곱이 생성
- 애트리뷰트 이름 앞에 릴레이션 이름을 사용하는 것이 바람직하고, 두 릴레이션의 조인 애트리뷰트 이름이 동일하다면 반드시 애트리뷰트 이름 앞에 릴레이션 이름을 사용해야 함

```sql
SELECT EMPNAME, DEPTNAME
FROM EMPLOYEE AS E, DEPARTMENT AS D
WHERE E.DNO = D.DEPTNO;
```
- 최종 결과 릴레이션읜 EMPNAME과 DEPTNAME을 프로젝션한 것

### 자체 조인
- 한 릴레이션에 속하는 투플을 동일한 릴레이션에 속하는 투플과 조인
- 실제로 한 릴레이션이 접근되지만 FROM절에 두 릴레이션이 참조되는 것처럼 나타내기 위해서 그 릴레이션에 대한 별칭을 두 개 지정해야함

```sql
SELECT E.EMPNAME M.EMPNAME
FROM EMPLOYEE E, EMPLOYEE M
WHERE E.MANAGER = M.EMPNO;
```

EX) 조인과 ORDER BY(정렬)의 결합
- 모든 사원에 대해서 소속 부서 이름, 사원의 이름, 직급, 급여를 검색하라. 부서 이름에 대해 오름차순, 부서이름이 같은 경우엔 SALARY에 대해 내림차순 정렬
```sql
SELECT DEPTNAME, EMPNAME, TITLE, SALARY
FROM EMPLOYEE E, DEPARTMENT D
WHERE E.DNO = D.DEPTNO
ORDER BY DEPTNAME, SALARY DESC;
```

### 중첩질의
- 외부 질의 WHERE절에 다시 SELECT ... FROM ... WHERE 형태로 포함된 SELECT문
- 부질의(서브쿼리)라고도함
- INSERT, DELETE, UPDATE문에도 사용될 수 있음

1) 한 개의 스칼라값이 반환되는 경우
EX) 박영권과 같은 직급을 갖는 모든 사원들의 이름과 직급을 검색
```sql
SELECT EMPNAME, TITLE
FROM EMPLOYEE
WHERE TITLE =
	( SELECT TITLE
      FROM EMPLOYEE
      WHERE EMPNAME = '박영권');
```

2) 한 개의 애트리 뷰트로 이루어진 릴레이션이 반환
- 중첩 질의 결과로 한 개의 애트리뷰트로 이루어진 다수의 투플들이 반환
- 외부 질의 WHERE절에서 
	`IN` : 한 애트리뷰트가 값들의 집합에 속하는가? 참, 거짓
    (EX) SELECT EMPNAME
    	 FROM EMPLOYEE
         WHERE DNO IN
         (SELECT DEPTNO
          FROM DEPARTMENT
          WHERE DEPTNAME = '영업' OR DEPTNAME = '개발');
    ☑️ `IN`을 사용한 질의를 중첩 질의를 사용하지 않고 조인질의로 나타냄
    (EX) SELECT EMPNAME
    	 FROM EMPLOYEE E, DEPARTMENT D
         WHERE E.DNO = D.DEPTNO
         	AND (D.DEPTNO = '영업' OR D.DEPTNAME = '개발');
    
    `ANY`: 한 애트리뷰트가 값들의 집합에 속하는 `하나 이상의 값`들과 어떤 관계를 갖는가를 테스트 하는 경우 (하나라도 맞으면 참, 모두 틀리면 거짓), `SOME`과 동일
    (EX) SELECT EMPNAME, DNO
         FROM EMPLOYEE
         WHERE DNO = ANY
         	(SELECT DEPTNO
            FROM DEPARTMENT
            WHERE DEPTNAME='영업' OR DEPTNAME='개발');
    `ALL`: 한 애트리뷰트가 값들의 집합에 속하는 `모든 값`들과 어떤 관계를 갖는가를 테스트 하는 경우
    (EX) SELECT EMPNAME, DNO
         FROM EMPLOYEE
         WHERE DNO < ALL
         	(SELECT DEPTNO
            FROM DEPARTMENT
            WHERE DEPTNAME='기획' OR DEPTNAME='개발');
    (EX) SELECT EMPNAME, DNO
         FROM EMPLOYEE
         WHERE DNO <> ALL
         	(SELECT DEPTNO
            FROM DEPARTMENT
            WHERE DEPTNAME='기획' OR DEPTNAME='개발');
            
3) 여러 애트리뷰드들로 이루어진 릴레이션이 반환되는 경우
`EXISTS`: 중첩 질의의 결과가 빈 릴레이션인지 여부를 검사, 빈 릴레이션이면 거짓이고 하나라도 값이 존재하면 참

```sql
(EX) SELECT EMPNAME
	 FROM EMPLOYEE E
     WHERE EXISTS
     	(SELECT *
        FROM DEPARTMENT D
        WHERE E.DNO = D.DEPTNO
        	AND (DEPTNAME = '영업' OR DEPTNAME = '개발'));
      
```

### 상관 중첩 질의
- 중첩 질의의 WHERE절에 있는 프레디키트에서 외부 질의에 선언된 릴레이션의 일부 애트리뷰트를 참조하는 질의
- 중첩 질의의 수행 결과가 단일 값이든, 하나 이상의 애트리뷰트로 이루어진 릴레이션이든 외부 질의로 한 번만 결과를 반환하면 `상관 중첩 질의`가 아님
- 상관 중첩 질의에서는 외부 질의를 만족하는 각 투플이 구해진 후에 중첩 질의가 수행되므로 상관 중첩 질의는 외부 질의를 만족하는 투플 수 만큼 여러 번 수행가능

## INSERT문
- 기존의 릴레이션에 투플을 삽입
- 릴레이션에 한 번에 한 투플을 삽입하는 INSERT문
INSERT 
INTO 릴레이션(애트리뷰트1, ... , 애트리뷰트n)
VALUES (값1, ... , 값n);

EX) 
```sql
INSERT INTO DEPARTMENT (DEPTNO, DEPTNAME) VALUES(6, '인사');
```

EX) EMPLOYEE 릴레이션에서 급여가 3,000,000이상인 사원의 이름, 직급, 급여를 검색하여 HIGH_SALARY라는 릴레이션에 삽입하라. HIGH_SALARY 릴레이션은 이미 생성되어있다고 가정 

```sql
INSERT INTO HIGH_SALARY (ENAME, TITLE, SAL)
SELECT EMPNAME, TITLE, SALARY
FROM EMPLOYEE
WHERE SALARY >= 3000000;
```

## DELETE문
- 한 릴레이션으로부터 한 개 이상의 투플들을 삭제
- DELECT문의 구문
DELECT FROM 릴레이션 WHERE 조건;
EX)
```sql
DELETE FROM DEPARTMENT
WHERE DEPTNO = 4;
```
## UPDATE문
- 한 릴레이션에 들어있는 투플들의 애트리뷰트 값을 수정
UPDATE 릴레이션 SET 애트리뷰트 = 값 또는 식 WHERE 조건;
EX)
사원번호가 2106인 사원의 소속 부서를 3번 부서로 옮기고, 급여를 5% 올려라

UPDATE EMPLOYEE
SET DNO = 3, SALARY =SALARY * 1.05
WHERE EMPNO = 2106;
