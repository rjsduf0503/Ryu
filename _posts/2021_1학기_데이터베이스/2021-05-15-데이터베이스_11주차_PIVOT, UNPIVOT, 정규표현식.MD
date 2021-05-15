---
######layout: post
author: 류건열
#title: Database_Assignment
---


- 11주차 과제 - PIVOT, UNPIVOT, 정규표현식
- 실습 환경 : Oracle
- 해결 날짜 : 2021-05-15

- 과제
    - 11-1. [예제1]~[예제5]를 참고하여 PLAYER 테이블에서 팀별로 각 포지션별 선수의 수와 평균 신장(키)을 구하는 뷰를 생성하고 생성된 뷰의 전체 데이터를 조회하는 SQL문을 작성하라. (단, PIVOT절을 사용하고 팀 ID순으로 정렬한다.) [2점]
        
        - SQL문 :     

        ```sql
        CREATE VIEW V_PLAYER_HEIGHT AS
        SELECT *
        FROM (SELECT TEAM_ID, POSITION, HEIGHT FROM PLAYER)
        PIVOT (AVG(HEIGHT) 평균키, COUNT(*) "선수의 수"
            FOR POSITION IN ('DF' 수비수, 'GK' 골키퍼, 'FW' 공격수, 'MF' 미드필더))
        ORDER BY TEAM_ID;
        SELECT * FROM V_PLAYER_HEIGHT;
        ```

        - 실행 결과 : 

        ![image](https://user-images.githubusercontent.com/34560965/118362406-62c48680-b5ca-11eb-9c0f-4479b392a602.png)
        ![image](https://user-images.githubusercontent.com/34560965/118362407-648e4a00-b5ca-11eb-89ab-d9e6aef31091.png)

    - 11-2. [예제6]~[예제11]을 참고하여 [과제11-1]에서 생성한 뷰의 전체 데이터 조회 결과 중 각 팀에서 포지션이 ‘MF’인 선수들의 정보(선수의 수, 평균 신장(키))를 조회하는 SQL문을 작성하라. (단, UNPIVOT절을 사용하고 팀 ID순으로 정렬한다.) [2점]

        - SQL문 : 

        ```sql
        SELECT TEAM_ID 팀명, COL, DATA
        FROM V_PLAYER_HEIGHT
        UNPIVOT (DATA
            FOR COL IN ("미드필더_선수의 수", "미드필더_평균키"))
        ORDER BY TEAM_ID;
        ```

        - 실행 결과 : 

        ![image](https://user-images.githubusercontent.com/34560965/118362463-8be51700-b5ca-11eb-9bd5-4e8b464f7425.png)

    - 11-3. EMP 테이블에서 이메일 주소가 ‘소문자+숫자@소문자+숫자.소문자’ 패턴인 직원의 직원번호, 직원이름, 직무, 이메일 주소를 출력하는 SQL문을 작성하라. (단, REGEXP_LIKE 함수를 사용하며 POSIX 연산자와 PERL 정규 표현식 연산자 두 가지 방법을 모두 사용하여 출력한다.) [2점]

        - SQL문 : 

        ```sql
        -- with POSIX
        SELECT EMPNO 직원번호, ENAME 직원이름, JOB 직무, EMAIL "이메일 주소"
        FROM EMP
        WHERE REGEXP_LIKE(EMAIL, '^[[:lower:][:digit:]]+@[[:lower:][:digit:]]+\.[[:lower:]]+$');

        --with PERL
        SELECT EMPNO 직원번호, ENAME 직원이름, JOB 직무, EMAIL "이메일 주소"
        FROM EMP
        WHERE REGEXP_LIKE(EMAIL, '^\w+?@\w+?\.(\w)+?$');
        ```

        - 실행 결과 : 

        ![image](https://user-images.githubusercontent.com/34560965/118362491-ab7c3f80-b5ca-11eb-871a-0db91b993d41.png)
        ![image](https://user-images.githubusercontent.com/34560965/118362494-ae773000-b5ca-11eb-831e-925163d87c33.png)


    - 11-4. EMP 테이블에서 모든 직원의 직원번호, 직원이름, 기존의 핸드폰 번호, ‘숫자-숫자-숫자’ 패턴으로 변경한 핸드폰 번호를 출력하는 SQL문을 작성하라. (단, REGEXP_REPLACE 함수를 사용하며, POSIX 연산자와 PERL 정규 표현식 연산자 두 가지 방법을 모두 사용하여 출력한다.) [2점]

        - SQL문 : 

        ```sql
        -- with POSIX
        SELECT EMPNO 직원번호, ENAME 직원이름, MOBILE "기존 핸드폰 번호", 
                REGEXP_REPLACE(MOBILE, '([[:digit:]]{3})\.([[:digit:]]{4})\.([[:digit:]]{4})', '\1-\2-\3') AS "변경한 핸드폰 번호"
        FROM EMP;

        -- with PERL
        SELECT EMPNO 직원번호, ENAME 직원이름, MOBILE "기존 핸드폰 번호", 
                REGEXP_REPLACE(MOBILE, '(\d{3}?)\.(\d{4}?)\.(\d{4}?)', '\1-\2-\3') AS "변경한 핸드폰 번호" 
        FROM EMP;
        ```

        - 실행 결과 : 

        ![image](https://user-images.githubusercontent.com/34560965/118362527-cf3f8580-b5ca-11eb-9c88-28dd05273abd.png)
        ![image](https://user-images.githubusercontent.com/34560965/118362532-d1a1df80-b5ca-11eb-9333-08cd1426b229.png)


    - 11-5. EMP 테이블에서 개인 홈페이지가 있는 직원들의 직원번호, 직원이름, 기존의 개인 홈페이지 주소, 메인 URL만 분리한 개인 홈페이지 주소 (예시: http://www.naver.com/)를 출력하는 SQL문을 작성하라. (단, REGEXP_SUBSTR 함수를 사용하며 POSIX 연산자와 PERL 정규 표현식 연산자 두 가지 방법을 모두 사용하여 출력하고 개인 홈페이지 주소가 없는 직원은 제외한다.) [2점]

        - SQL문 : 

        ```sql
        -- with POSIX
        SELECT EMPNO 직원번호, ENAME 직원이름, PERSONAL_HOMEPAGE "기존 홈페이지 주소",
                REGEXP_SUBSTR(PERSONAL_HOMEPAGE, 'http://([[:alnum:]]+\.?){3,4}\/') "메인 URL"
        FROM EMP
        WHERE PERSONAL_HOMEPAGE IS NOT NULL;

        -- with PERL
        SELECT EMPNO 직원번호, ENAME 직원이름, PERSONAL_HOMEPAGE "기존 홈페이지 주소",
                REGEXP_SUBSTR(PERSONAL_HOMEPAGE, 'http://(\w+\.?){3,4}\/') "메인 URL"
        FROM EMP
        WHERE PERSONAL_HOMEPAGE IS NOT NULL;
        ```

        - 실행 결과 : 

        ![image](https://user-images.githubusercontent.com/34560965/118362597-0f9f0380-b5cb-11eb-89a4-0ae6c7eddc1f.png)
        ![image](https://user-images.githubusercontent.com/34560965/118362601-12015d80-b5cb-11eb-813a-c31d82429d17.png)


