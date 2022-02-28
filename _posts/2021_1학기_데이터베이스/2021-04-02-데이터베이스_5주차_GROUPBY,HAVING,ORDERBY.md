---
layout: post
author: 류건열
title: 2021년 1학기 database 5주차
categories: database
tags: [cnu, database, 과제]
---

- 5주차 과제 - GROUPBY, HAVING, ORDERBY
- 실습 환경 : Oracle
- 해결 날짜 : 2021-04-02

- 과제

  - 5-1) [예제1-2] 변형하여 PLAYER 테이블의 TEAM_ID별 선수들의 평균 키와 평균 몸무게를 출력하는 SQL 문과 그 결과를 출력하라.(단 평균키, 평균 몸무게는 소수점 1째 자리까지 표시하세요.) [1점]

    - SQL문 :

    ```sql
    SELECT TEAM_ID "팀 아이디", ROUND(AVG(HEIGHT), 1) "평균 키", ROUND(AVG(WEIGHT), 1) "평균 몸무게"
    FROM PLAYER
    GROUP BY TEAM_ID;
    ```

    - 실행 결과 :

    ![image](https://user-images.githubusercontent.com/34560965/115106009-43234980-9f9d-11eb-94dd-d347c7218ec2.png)

  - 5-2) [예제2-2]를 변형하여 EMP 테이블에서 역할(JOB)별 직원수, 연봉 총합, 연봉 평균, 연봉의 표준편차를 구하는 SQL문을 작성하고 그 결과를 출력하라. (단, 연봉은 월 급여와 커미션을 포함하여 계산한다. 또한, 소수점 이하 자리는 모두 버리고, 평균 연봉이 30,000불을 초과하는 JOB만 출력한다.) [1점]

    - SQL문 :

    ```sql
    SELECT JOB 역할
        ,COUNT(JOB) 직원수
        ,SUM(SAL)*12+SUM(NVL(COMM,0)) 연봉총합
        ,ROUND(AVG(SAL)*12+AVG(NVL(COMM,0)),0) 연봉평균
        ,ROUND(STDDEV(SAL*12 + NVL(COMM,0)),0) 연봉의표준편차
    FROM EMP
    GROUP BY JOB
    HAVING ROUND(AVG(SAL)*12+AVG(NVL(COMM,0)),0) > 30000;
    ```

    - 실행 결과 :

    ![image](https://user-images.githubusercontent.com/34560965/115106024-5209fc00-9f9d-11eb-80e0-a744d4d426ef.png)

  - 5-3) [예제1-1]을 참고하여 EMP 테이블에서 역할(JOB)이 ‘CLERK’ 이거나 ‘MANAGER’인 직원의 역할, 직원이름, 입사일, 월급여를 출력하는 SQL문을 작성하고 그 결과를 출력하라. (단, JOB, 입사일(내림차순), 월급여(오름차순) 순으로 정렬하여 출력한다.) [1점]

    - SQL문 :

    ```sql
    SELECT JOB 역할, ENAME 직원이름, HIREDATE 입사일, SAL 월급여
    FROM EMP
    WHERE JOB IN ('MANAGER', 'CLERK')
    ORDER BY JOB DESC, HIREDATE DESC, SAL ASC;
    ```

    - 실행 결과 :

    ![image](https://user-images.githubusercontent.com/34560965/115106053-8b426c00-9f9d-11eb-8cce-54f0c5a58610.png)

  - 5-4) [예제2-*]를 참고하여 PLAYER 테이블에서 E_PLAYER_NAME이 없는 사람들은 제외하고, 팀별로 소속선수의 수를 조회하되 소속 선수의 수가 20명 이상인 상위 5개 미만의 팀만 출력하는 SQL문을 작성하고 그 결과를 출력하라.(단 WHERE, ORDER BY, GROUP BY, HAVING를 모두 사용하세요.)[2점]

    - SQL문 :

    ```sql
    SELECT T."팀명", T."소속선수의 수"
    FROM (SELECT TEAM_ID AS 팀명, COUNT(E_PLAYER_NAME) AS "소속선수의 수"
        FROM PLAYER
        GROUP BY TEAM_ID
        HAVING COUNT(E_PLAYER_NAME) >= 20
        ORDER BY COUNT(E_PLAYER_NAME) DESC
        ) T
    WHERE ROWNUM < 5;
    ```

    - 실행 결과 :

    ![image](https://user-images.githubusercontent.com/34560965/115106066-9dbca580-9f9d-11eb-8a76-8895048650b3.png)
