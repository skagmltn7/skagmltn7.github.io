---
title: "[TIL]Webclient 비동기로 리팩토링하기"
author: heesu
date: 2024-03-20 20:27:00+0900
categories: [TIL]
tags: []
---
## 발생한 ISSUE 및 해결 방법

### ISSUE1. 동기식으로 작동하던 webclient 비동식으로 바꾸니 빨라져서 오류…!

❓**문제상황**

- 시장 정보에 대한 정보 하나하나를 동기식으로 처리하여 시장정보 359개의 좌표를 가져오는데 걸린시간 16.63초

![동기](https://github.com/skagmltn7/skagmltn7/assets/133394749/71a94bff-241a-4a15-a69e-6df547c42818)


```java
public KakaoPlotResponse getKakaoPlot(String search, String keyword) {
  StringBuilder url = new StringBuilder(KAKAOMAP_URL)
      .append(search)
      .append(".json?query=")
      .append(keyword);
      
  return WebClient.create(url.toString())
    .get()
    .header("Authorization", "KakaoAK " + KAKAO_API_KEY)
    .retrieve()
    .bodyToMono(KakaoPlotResponse.class)
    .block();
}
```

- block을 하는 순간 결과값이 나올때까지 블락된다. webclient request를 보내고 block을 하기때문에 결과 하나하나씩 기다려야한다는 점이 있었다. 359개도 16.63초 걸리는데 더 많아진다면??? 시장 정보 가져오는데 n분이상이 걸릴 수도 있다.
- 그래서 시장 주소의 좌표를 비동기식으로 좌표 정보를 가져오기로 했다.
  - 시장 정보의 순서는 상관없고 빨리 가져오기 위해서 비동기로 외부 API가져오기로 결정

```java
public Mono<KakaoPlotResponse> getKakaoPlot(String search, String keyword) {
  StringBuilder url = new StringBuilder(KAKAOMAP_URL)
      .append(search)
      .append(".json?query=")
      .append(keyword);

  return WebClient.create(url.toString())
      .get()
      .header("Authorization", "KakaoAK " + KAKAO_API_KEY)
      .retrieve()
      .bodyToMono(KakaoPlotResponse.class)
      .flatMap(res -> res.isEmpty() ? Mono.empty() : Mono.just(res));
}

private <T extends MarketAddr> Mono<Market> matchMarketPlot(T market) {
  return getKakaoPlot("address", market.getAddr())
      .switchIfEmpty(getKakaoPlot("keyword", market.getName()))
      .map(res -> Market.from(MarketInfoResponse.of(market, res)));
}

public <T extends MarketAddr> List<Market> matchMarketPlotList(List<T> markets, int delay) {
  return Flux
      .fromIterable(markets)
      .flatMap(this::matchMarketPlot)
      .collectList()
      .block();
}
```

- 일단 비동기로 바꾸기위해 response를 Mono상태로 놔두었다. 시장정보가 다 모이면 flux로 모아서 block시킨다면 비동기로 처리할 수 있다.
- 위 코드와 비교했을때 또 달라진 점이 있는데 바로 flatMap부분이다. 이부분은 시장 주소를 쳐서 좌표를 안나오는 경우가 있었다. 그런 경우에는 시장의 이름으로 좌표를 나오도록 처리하기 위해 검색정보가 없으면 empty를 반환하여 후에 `switchIfEmpty` 로 처리하도록 하였다.
  - swtichIfEmpty
    - Mono나 Flux가 empty면 처리되는 if/else 분기문이라 생각하면된다.
    - empty이면 실행되는 조건문

```java
private <T extends MarketAddr> Mono<Market> matchMarketPlot(T market) {
  return getKakaoPlot("address", market.getAddr()) // 시장 주소로 좌표가져오기
      // 시장 주소에 매칭된 좌표가 없으면 시장 이름으로 좌표가져오기
      .switchIfEmpty(getKakaoPlot("keyword", market.getName()))
      // 좌표가 매치된 시장을 엔티티로 매칭시킴		
      .map(res -> Market.from(MarketInfoResponse.of(market, res)));
}
```

- 카카오 지도 API에서 시장의 주소에 대응되는 좌표를 가져오고있었다. 비동기처리로 api를 요청해서 너무 많은 request보내서 429가 뜬다.

![429에러](https://github.com/skagmltn7/skagmltn7/assets/133394749/cedb3133-44fc-4571-9f5b-c19d85e51588)

- request에 딜레이를 줘서 속도조절을 해야했다.

❗**해결방법**

```java
public <T extends MarketAddr> List<Market> matchMarketPlotList(List<T> markets, int delay) {
  return Flux
      .fromIterable(markets)
      .delayElements(Duration.ofMillis(delay))
      .flatMap(this::matchMarketPlot)
      .collectList()
      .block();
}
```

- Flux의 delayElements를 추가하여 각 요청이 들어가기전 0.001초 지연 후 요청하도록 delayElements를 추가하였다.
- 그러자!!!!!!!!!!!!!!!!!!!!!

![비동기](https://github.com/skagmltn7/skagmltn7/assets/133394749/7fd3376b-9054-4069-812a-0990979a6000)

**👩‍💻깨달은점**

- 너무 빨라도 안된다… 외부서버에서 우리 서버를 DDOS 공격으로 취급할 수 있다..

**출처**

https://www.baeldung.com/spring-webclient-limit-requests-per-second

