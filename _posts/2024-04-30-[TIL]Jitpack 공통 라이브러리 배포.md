---
title: "[TIL]Jitpack 공통 라이브러리 배포"
author: heesu
date: 2024-04-30 19:12:00+0900
categories: [TIL]
tags: []
---

  * [서버 비동기 통신을 위한 ApiClient](#서버-비동기-통신을-위한-apiclient)
  * [데이터 조회 삽입하는 RedisUtils](#데이터-조회-삽입하는-redisutils)
  * [ResponseEntityFactory](#responseentityfactory)
  
## 오늘 진행한 작업

### 🎉완료

- [Jitpack](https://jitpack.io/#SamsungSignature/signature-common) 으로 이번 프로젝트에서 사용할 공통 라이브러리 배포

## 발생한 ISSUE 및 해결 방법

### ISSUE1. 서비스에서 공통으로 사용되는 모듈을 반복적으로 구현해야할까?

❓**문제상황**

- 이번에 Signature서버, Wallet서버, VAN서버를 각각 띄운다.
- 이때 공통되는 모듈이 있는데 똑같은 코드를 복붙하는게 비효율적이라고 생각되었다. 그래서 공통모듈을 배포하여 라이브러리로 사용하려고한다.
- 그렇다면 어**떻게 자바코드를 외부 라이브러리로 배포할 수 있을까?**

❗**해결방법**

### Jitpack

<aside>
🔖 JitPack is a novel package repository for JVM and Android projects. It builds Git projects on demand and provides you with ready-to-use artifacts (jar, aar).

If you want your library to be available to the world, there is no need to go through project build and upload steps. All you need to do is push your project to GitHub and JitPack will take care of the rest. That’s really it!

</aside>

- 간단하게 말하자면, JVM와 안드로이드 jar파일을 깃허브에서 릴리즈하면 외부 라이브러리로 등록할 수 있게 해준다.
- github에 올리고 tag를 생성하면 jitpack에서 자동적으로 라이브러리를 생성해준다.

---

### 서버 비동기 통신을 위한 ApiClient

- **GET method**

```java
	/**
	 * Sends a GET request and logs the completion.
	 *
	 * @param webClient the WebClient instance used to send the request.
	 * @param uri the URI to send the request to.
	 * @param returnType the request body.
	 * @param <T> the type of the request body.
	 */
	public static <T> Mono<T> get(
		WebClient webClient,
		String uri,
		Class<T> returnType
	) {
		return webClient
			.get()
			.uri(uri)
			.retrieve()
			.bodyToMono(returnType)
			;
	}
```

- 서비스마다 싱글톤패턴으로 webclient생성 후 사용하는 GET 메소드
- 일단은 결과가 0~1개일 것같아 Mono로 구현해놓았는데 추후에 더많은 반환값이 필요하면 Flux도 추가할 예정이다.



- **Post method**

```java
	/**
	 * Sends a POST request and logs the completion.
	 *
	 * @param webClient the WebClient instance used to send the request.
	 * @param uri the URI to send the request to.
	 * @param body the request body.
	 * @param <T> the type of the request body.
	 * @param <U> the type of the response body.
	 */
	public static <T, U> Mono<U> post(
		WebClient webClient,
		String uri,
		T body,
		Class<U> returnType
	) {
		return webClient
			.post()
			.uri(uri)
			.bodyValue(body)
			.retrieve()
			.bodyToMono(returnType)
			;
	}
```



### 데이터 조회 삽입하는 RedisUtils

- 마찬가지로 모든 서버에서는 redis에 접근한다. 이를 하나로 통합하면 key를 설정하기도 편해 구현해보았다.



- **Redis의 데이터 조회**

```java
	public static <T> T get(RedisTemplate<byte[], byte[]> redisTemplate,
		ObjectMapper objectMapper,
		Class<T> classType,
		Object... keys
		) {
		String key = makeKey(keys);
		byte[] redisValue = redisTemplate.opsForValue()
																			.get(key.getBytes(StandardCharsets.UTF_8));
		if (ObjectUtils.isEmpty(redisValue)) {
			throw new SignatureException(ServerErrorCode.BAD_REQUEST);
		}
		return readValue(objectMapper, redisValue, classType);
	}
```



- **Redis의 데이터 삽입**

```java
	public static <T> void put(RedisTemplate<byte[], byte[]> redisTemplate,
		ObjectMapper objectMapper,
		T value,
		Object... keys) {
		try {
			redisTemplate.opsForValue()
									.set(makeKey(keys).getBytes(StandardCharsets.UTF_8),
					objectMapper.writeValueAsString(value)
											.getBytes(StandardCharsets.UTF_8));
		} catch (JsonProcessingException ex) {
			throw new SignatureException(ServerErrorCode.BAD_REQUEST);
		}
	}
```

- byte형태의 value를 dto에 매칭할때 JsonProcessingException이 발생할 수 있어 try-catch처리



- **정해지지 않은 길이의 key값을 만들어주는 makeKey**

```java
	private static String makeKey(Object... keys) {
		StringBuilder sb = new StringBuilder();
		for (Object key : keys) {
			sb.append(key.toString());
		}
		return sb.toString();
	}
```

- 이 메소드는 이 모듈에서만 사용되기 때문에 캡슐화



- **코드 depth를 줄이기 위한 readValue**

```java
	private static <T> T readValue(ObjectMapper objectMapper,
		byte[] redisValue,
		Class<T> classType) {
		try {
			return objectMapper.readValue(redisValue, classType);
		} catch (IOException ex) {
			throw new SignatureException(ServerErrorCode.BAD_REQUEST);
		}
	}
```

이외에도 **백엔드, 프론트엔드의 api 응답형태를 통일하기 위하여 ResponseEntityFactory를 구현**하였다.



### ResponseEntityFactory

```java
	public static ResponseEntity<MessageBody<Void>> ok(
		String message
	) {
		return ResponseEntity.ok(MessageBody.of(message, null));
	}

	public static <T> ResponseEntity<MessageBody<T>> ok(
		String message,
		T body
	) {
		return ResponseEntity.ok(MessageBody.of(message, body));
	}

	public static ResponseEntity<MessageBody<Void>> created(
		String message
	) {
		return ResponseEntity.status(HttpStatus.CREATED)
			.body(MessageBody.of(message, null));
	}

	public static <T> ResponseEntity<MessageBody<T>> created(
		String message,
		T body
	) {
		return ResponseEntity.status(HttpStatus.CREATED)
			.body(MessageBody.of(message, body));
	}

	public static ResponseEntity<MessageBody<Void>> status(
		int status,
		String message
	) {
		return ResponseEntity.status(status)
			.body(MessageBody.of(message, null));
	}

	public static <T> ResponseEntity<MessageBody<T>> status(
		int status,
		String message,
		T body
	) {
		return ResponseEntity.status(status)
			.body(MessageBody.of(message, body));
	}

	public static ResponseEntity<MessageBody<Void>> status(
		HttpStatus status,
		String message
	) {
		return ResponseEntity.status(status)
			.body(MessageBody.of(message, null));
	}

	public static <T> ResponseEntity<MessageBody<T>> status(
		HttpStatus status,
		String message,
		T body
	) {
		return ResponseEntity.status(status)
			.body(MessageBody.of(message, body));
	}
```


👩‍💻**얻은점**

- 아무래도 공통 모듈이기 때문에 어떤 형태의 클래스가 들어올지 알 수 없다. 확장성을 열어두기 위해서 제네릭을 사용할 수 있었던 경험이었다.
- 또한, 다형성을 보장하기 위해 Overload를 써본 경험이었다.
