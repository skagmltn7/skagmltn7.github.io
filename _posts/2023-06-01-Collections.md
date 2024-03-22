---
title: "Collection"
author: heesu
date: 2023-06-01 06:11:00+0900
categories: [Studying, java]
tags: [Collections, interface, List, ArrayList, Map, HashMap, LinkedList, Set, HashSet, TreeSet, TreeMap, stack, queue, hashing]
---

# Collections framework<br>
`Collections` (여러 객체를 모아놓은 것) + `framework` (표준화된 프로그래밍 방식)으로 객체를 다룰 수 있는 다양한 클래스들이 담겨있다. java.util패키지에 포함되어 있다.<br><br>
![Alt text](https://user-images.githubusercontent.com/133394749/242412123-edf5cf2c-7a2b-4bf9-8886-77ed763e0aa1.jpg)

Collection의 핵심 인터페이스로는 `List, Set`이 있고 관련된 인터페이스로는 `Map`이 있다. <br>



||List|Set|Map|
|:---:|:---:|:---:|:---:|
|중복|O|X|key: X<br>value: O|
|순서|O|X|X|




## List<br>
**List**는 순서가 있고 중복이 가능하다. 종류에는 ArrayList, LinkedList 등이 있다.<br>
### ArrayList<br>
- 인터페이스 List를 구현한 클래스이고 데이터 저장공간으로 배열을 사용한다.
- `stack`을 구현하기 좋다.<br>

#### ArrayList의 삭제 과정<br>
1. 삭제할 값의 인덱스 뒤 값들을 모두 복사하여 한 칸씩 이동시킨다.<br>
2. 맨 뒤의 값을 null 로 변경한다.<br>
3. ArrayList의 사이즈를 하나 감소시킨다.<br>

- 순차적으로 값을 제거하거나 추가할 때 1번 순서 생략<br>



||장점|단점|
|:---:|:---:|:---:|
||데이터 접근 시간<br>짧음|크기 변경X|
||구조 간단|비순차적인 추가,삭제<br>시간 걸림|


<br>

### LinkedList<br>
- ArrayList의 단점을 보완하기 위한 자료구조이다. 
- 불연속적으로 존재하는 데이터를 참조변수로 연결한다. 삭제, 추가는 참조변경으로 구현이 가능하다.
- `queue`를 구현하기 좋다.<br>

#### ArrayList vs LinkList<br>



||ArrayList|LinkList|
|:---:|:---:|:---:|
|순차적<br>추가/삭제|빠름|빠름|
|비순차적<br>추가/삭제|별도의 배열 복사<br>+<br>이동으로<br>느림|참조변경(2번/1번)으로<br>빠름|
|접근시간|빠름<br>(인덱스 사용)|느림<br>(처음부터 끝까지 확인)|




- Arrays의 static메소드, Comparable, Comparator인터페이스 사용가능<br><br>

## Set<br>
**Set**은 순서와 중복이 없다. 종류에는 HashSet, TreeSet 등이 있다.<br>

### HashSet<br>
- 객체를 저장하기 전에 이미 저장된 값인지 검사 후 객체를 저장한다. 객체 중복을 검사해야하기 떄문에 equals()와 hashCode()를 오버라이딩해야한다.
- 순서를 유지하려면 LinkedHashSet클래스를 사용하면 된다.<br>

### TreeSet<br>
- 범위 검색과 정렬에 유리한 이진 검색 트리이다.
- 중위순회를 통해 오름차순으로 노드를 방문할 수 있다.
<br>

#### TreeSet의 추가 과정<br>
1. 추가하는 노드의 값이 부모노드(루트)보다 크면 오른쪽, 작으면 왼쪽에 저장한다.
2. 노드의 자식이 없을때까지 1. 반복<br>

#### HashSet vs TreeSet<br>


||HashSet|TreeSet|
|:---:|:---:|:---:|
|추가/삭제|빠름|느림<br>(순서유지때문)|


<br><br>

## Map<br>
- 순서가 없고 key는 중복 불가, value는 중복 허용한다.
- Collection 인터페이스에 속하진 않지만 key와 value가 Collection타입이다.
- 해싱하여 데이터를 저장한다.
- 종류에는 HashMap과 TreeMap이 있다.
- 데이터를 key:value 형태로 저장한다.
- 해싱 기법으로 데이터를 저장하고 데이터에 접근한다.<br>

### hashing<br>
<img src="https://user-images.githubusercontent.com/133394749/242412119-7182f3b5-8c48-48dc-80a5-f20fdd42aacf.jpg" width="50%" height="50%" alt="">
<br>
키를 해시함수을 통해 해시코드(해시테이블의 인덱스)를 생성하여 해시테이블(배열 + 링크드리스트)에 저장한다.<br><br>

![Alt text](https://user-images.githubusercontent.com/133394749/242414956-f53ac191-4401-474f-a149-a93d604b2336.jpg)
<br>

1. 비순차적인 요소 추가/삭제 기능 개선
2. 검색기능 향상
3. 검색, 범위 검색, 정렬 기능
4. 순서유지