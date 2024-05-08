---
title: "[TIL] 삼성 시그니처: Api Gateway가 바라보는 건 local, 쏘는건 배포서버????"
author: heesu
date: 2024-05-08 11:23:00+0900
categories: [TIL]
tags: []
---

* [❓문제상황](#문제상황)
* [❗해결방법](#해결방법)
* [👩‍💻얻은점](#얻은점)

# ISSUE1 . Api Gateway가 바라보는 건 local, 쏘는건 배포서버????

### ❓**문제상황**

- hearbeat는 로컬에다가 보내고있고 Eureka에서 확인해도 local에 있는 서버를 체크하고있었다.
- signatrue → api gateway → wallet → signature로 와야하는데 api gateway → wallet에서 자꾸 Time out이 발생하거나 500이 떠버린다…

<br>

### ❗**해결방법**

- logging.level.root = debug를 이 api가 거치는 모든 서버에 걸어놓았다.
- 그러자…

```yml
Connect attempt to [/43.201.50.220:8330] failed.
```

# 월렛 서버가 배포서버다?????

<br>

- 설정파일을 뜯어보니 로컬 서버의 api gateway는 로컬에 있는 wallet을 가르키고있었는데 wallet은 배포서버의 eureka를 바라보고있었다…!
- 그래서 현재 내린 배포환경의 Eureka로 보내서 500이 뜬거였다.

```yml
eureka:
  instance:
    instance-id: ${spring.application.name}:${spring.application.instance_id:${random.value}}
    hostname: 43.201.50.220
```

<br>

### 👩‍💻**얻은점**

`설정의 중요성... 명심..또 명심...`
