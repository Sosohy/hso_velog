---
_id: d89cd080-b2e7-40b7-bf23-cab810d2118a
date: '2024-08-17'
last_modified: '2024-11-20'
title: JAVA - Virtual Thread
url: https://velog.io/@hso07202/JAVA-Virtual-Thread
---

## 스레드
- 프로그램 내에서 동시에 여러 작업을 처리할 수 있도록 지원하는 기본적인 단위
    
    → 프로세스의 작은 작업 단위
    
- 동시에 여러 작업을 병렬로 수행 가능
- 스레드는 운영체제의 스레드와 JVM 스레드 두 가지로 구분 가능함 
→ 자바에서는 주로 JVM스레드 사용

## Virtual Thread

- Java Virtual Machine (JVM)에서 실행되는 경량의 스레드
- JVM의 스케줄러에 의해 관리, 훨씬 적은 메모리와 리소스 소모
- 사용 이유
    - 동시성이 높은 애플리케이션에서 스레드 수를 많이 생성해도 성능을 유지
    - 컨텍스트 스위칭 비용이 저렴함 → 메모리 크기가 일반 스레드의 1%에 불과
- 장점
    - 기존 스레드보다 메모리와 CPU 자원을 훨씬 적게 사용함
    - 스레드 생성과 관리의 복잡성 감소
    - 복잡한 작업을 동기식 코드처럼 쉽게 작성 가능함 → 코드 가독성 높임, 복잡성 감소
- 자바19에서 시험 도입, 21에서 공식 기능으로 제공

- 기존 스레드 모델(Native Thread)
    - Java의 유저 스레드를 만들면 Java Native Interface(JNI)를 통해 커널 영역을 호출하여 OS가 커널 스레드를 생성하고 매핑하여 작업을 수행하는 형태
    - 스레드가 block 상태 되면 다른 자바 스레드로 Context Switch
    - 애플리케이션 코드가 플랫폼 스레드를 사용하면 실제로는 OS 스레드를 사용하는 것
        - 비용이 비쌈
    - 각각의 유저 스레드는 용량 차지 → 스레드가 많을 경우, 메모리 많이 차지함
    
- Virtual Thread
    - 플랫폼 스레드 위에서 여러 Virtual Thread가 번갈아 가며 실행되는 형태로 동작
    - JVM 내에서 관리됨
    - 기존 자바 스레드 == 플랫폼 스레드
    - 기본 스케줄러 : ForkJoinPool 사용
        - platform thread pool을 관리하고, Virtual Thread의 작업 분배 역할

## 동작 방식
![](https://velog.velcdn.com/images/hso07202/post/48ff94a3-086b-4d56-9347-4f8cf5ba7aae/image.png)

[출처 : Java의 미래, Virtual Thread | 우아한형제들 기술블로그]
- carrierThread → 기존 사용 스레드
1. virtual thread의 작업을 carrier thread의 workQueue에 push
2. forkJoinPool에 의해 work stealing 방식으로 carrier thread에 의해 처리
3. carrierThread 가 작업하다가 block 되는 작업들(IO, Sleep 등)이 있으면 park() 실행해서 잠시 멈춤
4. block 작업 완료되면 다시 carrier thread의 workQueue에 push

### 주의해야 할 점

- 생성 비용이 작아서 스레드 풀 만드는 게 낭비일 수 있음
- vitualThread가 너무 무거우면 컨텍스트 스위칭 비용이 늘어나 비효율적
- CPU작업만 수행할 경우 효율이 떨어짐
- 응답이 빠르지는 않음 → 처리량이 높을 뿐
- Synchronized, NativeMethod 지양하기 → carrierThread가 같이 대기에 걸려서 사용 이유가 사라짐

### 참고 링크
[Java의 미래, Virtual Thread | 우아한형제들 기술블로그](https://techblog.woowahan.com/15398/)