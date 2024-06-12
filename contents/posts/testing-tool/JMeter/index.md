---
title: "[Spring] JMeter 사용법"
description: "JMeter란?, 테스트 방법"
date: 2024-06-12
update: 2024-06-12
tags:
  - Spring
  - JMeter
series: "JMeter 사용법"
---

## 서론
웹 애플리케이션 성능 테스트 툴은 웹 애플리케이션을 운영하기전 성능, 안정성 및 확장성을 평가하는 데 필수이며, 잘알려진 테스트 툴은 Java 오픈소스 `Apache JMeter`,  Node.js 오픈소스 `Artillery`, 네이버에서 Grinder를 이용해서 대규모 엔터프라이즈 환경용으로 만든 `nGrinder`, `Gatling`가 있습니다.

이 외에도 JavaScript 오픈소스 Grafana 통합을 통한 시각화를 제공하는 `k6`,  Python 오픈소스 `Locust` 등 각각의 특징과 장점을 가지고 있어, 사용 목적과 환경에 따라 선택하여 사용되고 있습니다. 

> 최근 운영을 앞두고 있는 프로젝트의 웹 어플리케이션 성능 테스트를 진행하면서 웹 애플리케이션 성능 테스트 툴로 Java 오픈소스 `Apache JMeter`를 선택하였고 선택하게된 이유와 분석 내용을 포스팅 하겠습니다.

## JMeter란?
![JMeter란?](./1.PNG)

Apache JMeter는 웹 애플리케이션 및 다양한 프로토콜에 대해 부하 테스트와 성능 측정을 수행할 수 있는 오픈 소스 도구입니다. GUI 기반의 사용이 편리한 인터페이스를 제공하며, HTTP, HTTPS, FTP, JDBC, JMS, SOAP, REST 등 다양한 프로토콜을 지원한다. JMeter는 분산 테스트, 스크립트 기반 자동화, 확장 가능한 플러그인, 그리고 다양한 리포팅 및 분석 기능을 제공하여 웹 애플리케이션의 성능을 종합적으로 테스트할 수 있다.

## JMeter 실행 방법
cmd -> 압축푼 폴더 아래 bin 폴더로 이동 -> jmeter 실행
> ex) D:\apache-jmeter-5.6.3\bin

![JMeter 실행방법 1.cmd](./2.PNG)

아래와 같은 GUI 가 나온다.
![JMeter 실행방법 2.Apache JMeter](./3.PNG)

## JMeter 테스트

### JMeter 테스트 환경 유의사항
테스트 환경 분리해야한다. 실제 운영 환경과 테스트 환경을 분리하여 테스트 툴 JMeter와 테스트 대상 웹 어플리케이션이 사용하는 메모리 등 영향을 최소화 한다.

### JMeter 테스트 용어 정리
|항목|내용|
|-|-----|
|Thread Group|테스트에 사용될 쓰레드 개수, 쓰레드 1개당 사용자 1명
|Sampler|사용자의 액션 (예: 로그인, 게시물 작성, 게시물 조회 등)
|Listener|응답을 받아 리포팅, 검증, 그래프 등 다양한 처리
|Configuration|Sampler 또는 Listener가 사용할 설정 값 (쿠키, JDBC 커넥션 등)
|Assertion|응답 확인 방법 (응답 코드, 본문 내용 비교 등)


### 1. Test Plan 생성
File -> New -> Test Plan Name 설정

![JMeter 테스트 1. Test Plan 생성](./4.PNG)

### 2. Thread Group
테스트 쓰레드, 쓰레드 개수를 만드는데 소요되는 시간, 요청 횟수를 설정한다.

방금 만든 테스트에 오른쪽 클릭 -> Add -> Threads (Users) -> Thread Group

![JMeter 테스트 2. Thread Group](./5.PNG)


![JMeter 테스트 2. Thread Group - ](./6.PNG)

#### Action to be taken after a Sampler error

Action to be taken after a Sampler error에서 Error가 리턴됐을 때 어떻게 할 건지에 대한 설정을 할 수 있다.

|항목|내용|
|-|-----|
|Continue|오류가 발생해도 JMeter는 현재 스레드 그룹의 다음 샘플러를 계속 실행. 오류는 로그에 기록되지만, 이후 샘플러의 실행에는 영향을 미치지 않음
|Start Next Thread Loop|오류가 발생하면 현재 스레드 루프를 중단하고, 스레드 그룹 내의 다음 루프 반복을 시작. 이는 루프 컨트롤러나 스레드 그룹의 루프 카운트를 기반
|Stop Thread|오류가 발생한 스레드만 중지. 이 옵션을 선택하면 해당 스레드의 모든 샘플러 실행이 중단
|Stop Test|오류가 발생하면 전체 테스트가 즉시 중지됩니다. 모든 스레드와 샘플러의 실행이 중단
|Stop Test Now|"Stop Test"와 유사하지만, 이 옵션은 테스트를 가능한 한 빨리 중지. 모든 현재 실행 중인 샘플러를 강제로 중단시키고 즉시 종료
|Abort Test|테스트 실행을 즉시 중단하고 JMeter를 종료합니다. 이는 긴급 상황에서 사용되며, 모든 스레드와 샘플러가 즉시 중단

#### Thread Properties

5명의 유저가 1초만에 2번 반복해서 에러가 발생해도 계속 요청을 보낸다고 설정


|항목|내용|
|-|-----|
|Number of Threads|테스트에 사용할 가상 사용자(쓰레드)의 수를 설정
|Ramp-up period|쓰레드 개수를 만드는데 소요되는 시간
|Loop Count| 각 스레드가 테스트 계획을 몇 번 반복할지를 설정


## 인 메모리(In-Memory) 저장 방식의 문제점
redis의 in memory 저장 방식은 빠르기도 하지만 DB 서버에 장애가 발생할 경우 또는 서버 전원이 갑자기 꺼지면 안에 있는 데이터들이 삭제된다. 그래서 보통은 로그인 세션 같은, 서버가 꺼져서 날아가도 상관 없는 임시 데이터에 주로 쓰인다. 그리고 대부분 속도의 장점으로 사용되기 때문에 압축은 하지 않으며, 데이터에 비해 RAM 용량이 넉넉하지 않을 경우 가상 메모리를 쓰게 되어 역효과가 일어나기도 한다.

해당 저장 방식의 문제점과 시스템의 안정성을 위해 보완을 해야할 방법에대해서 정리한 글 => <br> [Persist Option 데이터 영구 저장하기](../persistence/index.md)

## Redis의 주요 특성
### Redis 용어 정리

|항목|내용|
|-|-----|
|Key-Value 스토어|Key-Value 를 저장할 수 있는 스토리지를 지원한다.|
|컬렉션 지원	|List, Set, Sorted Set, Hash 등의 자료구조를 지원한다.|
|Pub/Sub 지원|Publish/Subscribe 모델을 지원한다.|
|디스크 저장(Persistent Layer)|현재 메모리의 상태를 디스크로 저장할 수 있는 기능과 현재까지의 업데이트 내용을 로그로 저장할 수 있는 AOF 기능이 있다.|
|복제(Replication)|다른 노드에서 해당 내용을 복제할 수 있는 Master/Slave 구조를 지원한다.|
|복제(Replication)|이상의 기능을 지원하면서도 초당 100,000 QPS(Queries Per Second) 수준의 높은 성능을 지원한다.|

+ Key-Value 스토어
  - 기본적으로 Redis 는 Key-Value 스토어로 빠르고 간단하게 데이터를 가지고 올 수 있다.
+ 컬렉션 지원
  - Redis 는 여러가지 자료구조를 지원하므로 서비스에 맞는 설계를 할 때 유용하게 사용될 수 있다.
+ 디스크 저장
  - Redis 의 특징 중 하나는 현재 메모리의 상태를 디스크에 스냅샷 형태로 남길 수 있는 RDB 방식이 있다. 그러므로 Redis 를 restart 할 때 스냅샷에 있는 내용을 가지고 쉽게 복구할 수 있고, AOF(Append Only File) 형태로 Redis 에 변경 내역들을 모두 남기는게 가능해서 Write Behind 같은 기능을 지원하는 것도 가능하다.
+ 복제
  - Redis 는 Master/Slave 구조로 이용할 수 있어서 Master 의 읽기 부하를 Slave 로 나누는게 가능하다.
+ 빠른 성능
  - Redis 를 이용하는 가장 큰 이유는 성능이다. 초당 50,000 ~ 60,000 이상의 처리 속도가 필요하다면 어쩔 수 없이 Redis 나 Memcached 를 이용해야한다.

## Redis와 비교대상 Memcashed
Memcahed와 Redis와 비교대상으로 질문이 많은데 이젠에 살펴본 후 머릿속에 남아있던 기억은 사실상 Redis가 더많은 기능을 제공하고있고 단순히 Memcahed에 비해 Redis가 추가적으로 제공하는 기능이 많았다고 기억하고 있습니다.
블로그를 작성하게되면서 더욱 자세히 알게되었고 리마인드하며 내용을 정리 했습니다.

```
Memcached 는 캐싱 솔루션이고 Redis 는 저장소의 개념까지 추가된 것 입니다.
```
캐싱이란 말은 빠른 응답을 위해 결과를 저장해주는 솔루션을 말하고 언제든지 사라져도 상관없습니다. 하지만 저장소의 개념은 데이터가 보존되어야 한다는 의미입니다. 그래서 Redis 는 RDB 나 AOF 같은 기능을 지원하기도 합니다.

그리고 추가로 Redis 는 Memcached 에 없는 여러가지 자료구조를 지원해서 개발자의 생산성을 높여주는 일도 해줍니다.

### Redis 와 Memcached 의 장단점 비교

|기능|Redis|Memcached|
|-|-----|----|
|속도|초당 100,000 QPS 이상|초당 100,000 QPS 이상|
|자료구조|List, Set, Sorted Set, Hash 지원|Key-Value 만 지원|
|안정성|특성을 잘못 이해할 경우에 장애가 발생할 수 있다.|장애가 거의 없다.|
|응답 속도의 균일성|Memcached 에 비해서 응답속도의 균일성이 떨어질 수 있다.|전체적으로 응답속도는 균일하다.|

응답 속도의 균일성 같은 경우가 Redis 와 Memcached 가 차이나는 이유는 메모리 할당 구조가 다르기 떄문인데 Redis 같은 경우는 Jemalloc() 을 사용해 메모리 할당을 하고 free() 를 통해서 메모리 할당을 지웁니다. 반면에 Memcached 는 slab 을 통해서 일정한 사이즈의 메모리를 균일하게 1MB 의 페이지로 자르고 그 안에 또 작은 사이즈 부터 큰 사이즈의 chunk() 를 일정하게 놔두는 식을 이용해 메모리 내부 단편화 현상은 있지만 외부 단편화 현상을 없도록 해서 메모리 관리를 조금 더 효율적으로 이용할 수 있어서 응답 속도가 비교적 균일합니다.


## 다음
[Persist Option 데이터 영구 저장하기](../persistence/index.md)