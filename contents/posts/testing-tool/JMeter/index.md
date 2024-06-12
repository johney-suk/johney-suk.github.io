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


### 3. Sampler

Thread Groupd이 해야할 행동을 정의하는 샘플이다.

Thread Group 우클릭 -> Add -> Sampler -> HTTP Request 클릭

![JMeter 테스트 3. Sampler](./7.PNG)

테스트로 통신을 시도한 Controller를 기준으로 기입

![JMeter 테스트 3. Sampler- settings](./8.PNG)

### 4. Listener
Sampler가 받아오는 리턴 값을 바탕으로 그래프, 레포팅을 만들어주는 Listener를 설정

## 다음
[Persist Option 데이터 영구 저장하기](../persistence/index.md)