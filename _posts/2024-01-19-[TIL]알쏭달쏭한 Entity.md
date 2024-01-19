---
title: "[TIL]알쏭달쏭한 Entity"
author: heesu
date: 2024-01-19 21:44:00+0900
categories: [TIL]
tags: []
---
# 

## 오늘 진행한 작업

### 🎉완료

- Entity 코드리뷰

### 🔥진행 중

- **JWT, OAuth2 개념 정리**
  - 왜 사용하는지, 어떻게 access/refresh Token 발급하고 관리하는지에 대한 로직 이해

    → Token 관리(로그아웃, 재발급 이전 토큰인지 확인)에 radis 사용할지 고민중…. 🤔

- **오늘 진행한 코드리뷰를 토대로 Entity refactoring**
  - @Converter적용해보기, 복합 유니크 엔티티에 적용하기

## 발생한 ISSUE 및 해결 방법

### ISSUE1. Entity와 Dto변환을 어디에서 해야할까? Controller? Service?

❓**문제상황**

- MVC패턴을 사용하면 Json형태로 request가 들어오게 된다.

  이를 ObjectMapper가 Json→Dto객체로 매칭(**역직렬화)**하여 Controller단으로 들어오는데

  **Dto → Entity, Entity → Dto는 어디서 변환하여 처리해야할지** 궁금했다.

  - 엔티티의 영속을  다루는 Repository에서 처리하지 않은 것은 당연하다 생각했다.

    Dto는 데이터 전송을 위한 객체인데 controller단에서만 사용해야하나?

    그렇다고 Entity를 controller에서 다룬다면 원치않게 변경될 수 있는데….?


❗**해결방법**

- `Entity와 Dto변환은 Service에서!`
  - controller에서 변환을 하다보면 entity가 변경되어서 트랜잭션이 일어날 것같다.
  - service가 View와 Model사이의 중계역할을 하기 때문에 모든 트랜잭션은 service에서 처리해야한다고 생각한다.
  - 정답은 정해져 있는것은 아니나 서비스단에서 맨 처음에 Dto→Entity변환 후 dto는 사용하지 않고 entity를 사용하는 방식으로 이번 프로젝트를 진행하려한다.

👩‍💻**얻은점**

## 내일 해야할 일

- **오전**
  - ‘뷰케어풀’ 최종 엔티티
  - 백엔드 아키텍처 init
- **오후**
  - 설계한 엔티티를 토대로 JWT기반 로그인 구현하기

    → 코드치면서 개념이해하기
