---
title: "[TIL] 삼성 시그니처: Spring Cloud MSA환경에서 서버간의 통신"
author: heesu
date: 2024-05-07 23:59:00+0900
categories: [TIL]
tags: []
---

* [❓문제상황](#문제상황)
* [❗해결방법](#해결방법)
  + [OpenFeign](#openfeign)

## ISSUE1. Spring Cloud MSA환경에서 서버간의 통신은 어떻게 해야할까?

### ❓**문제상황**

- `시그니처` 서비스는 API Gateway, 시그니처, 월렛, Auth, VAN으로 서버가 나뉘어져있다.
- 오늘 구현하려고했던 기능은 **유저가 대리결제 승인을 받은 카드 목록을 조회**하는 기능으로, 시그니처 → 월렛 → 시그니처로 돌아와야했다.
- 로직
  1. 유저가 카드 목록 조회
  2. API Gateway에서 시그니처로 조회api 연결
  3. 시그니처는 승인받은 카드pk조회
  4. 카드pk를 사용해서 월렛에서 카드정보 가져오기
- 4번 로직을 위해 서버간의 통신을 사용해야 했다.
- 그럼 시그니처 → 월렛을 하는 순간 시그니처→api gateway → 월렛이 된다.
- 외부 서버와 통신하는 HttpClient에는 **RestTemplate**, **WebClient**, **FeignClient가 있다. 이중 무엇을 사용해야할까?**

<br>

### ❗**해결방법**

- 제일 먼저 저번 프로젝트에서 사용했던 WebClient를 비동기식으로 가져오려고했다.
- 하지만 url매칭에 어려움이 있었고, Spring Cloud환경을 위해 Netflix에서 오픈소스로 제공하는 OpenFeign을 사용하려고한다.

<br>

## OpenFeign

- 선언적인 Http Client도구로 인터페이스에 어노테이션을 붙이면 HttpClient를 구현할 수 있다.
- Spring MVC의 GetMapping, PostMapping 등 Controller단에서 사용하는 어노테이션을 사용하여 가독성있게 외부 API호출을 할 수 있다.
- 우리는 Eureka서버가 있기 때문에 `@FeignClient` 에 Eureka에 등록한 서버 이름을 선언하면 자동적으로 그 서버로 연결할 수 있다.

<br>

오늘 마주한 문제는 시그니처에서 월렛으로 넘어갈때 Timeout이 자꾸 난다…

이 문제가 로컬 환경에서 진행하기 때문인지 문제점을 파악하지 못해서 내일 이에 대한 트러블 슈팅에 대해 작성해보려고한다.
