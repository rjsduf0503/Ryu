---
layout: post
author: 류건열
title: 알고리즘 문제 풀이(프로그래머스 - JadenCase 문자열 만들기)
categories: algorithm
tags: [cnu, algorithm, self-study, javascript]
---

# 프로그래머스 LEVEL 2(JadenCase 문자열 만들기)

  ![image](https://user-images.githubusercontent.com/34560965/189042207-2c5a8beb-2fbe-44c7-b89d-e193ca0e3c59.png)

  - 사용 언어 : javascript

  - 해결 날짜 : 2022-09-08

  - 해결 방법 :

    - 공백을 기준으로 split 후 단어마다 첫 글자는 대문자로, 나머지는 소문자로 변경 후 반환

  - 회고 : 
    - split 후 바로 map 적용하기

  - 코드

    ```javascript
    function solution(s) {
        const words = s.split(' ');
        for (const [index, word] of words.entries()) {
            let head = word.substring(0, 1).toUpperCase();
            let tail = word.substring(1).toLowerCase();
            words[index] = head + tail;
        }
        return words.join(' ');
    }
    ```
    
  - 출처: 프로그래머스 코딩 테스트 연습, https://school.programmers.co.kr/learn/challenges
