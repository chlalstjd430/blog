---
title: "[JAVA] - GC(Garbage Collection)"
date: 2020-06-24T18:44:55+09:00
categories: ["JAVA"]
---

# [JAVA] - GC(Garbage Collection)

GC(Garbage Collection)는 Java 프로세스가 동작하는 과정에서 불필요한 또는 더이상은 사용하지 않는 객체들을 메모리에서 제거함으로써, Java 프로세스가 한정된 메모리를 효율적으로 사용할 수 있게 해준다.


**stop the world**

GC 실행을 위해 JVM이 애플리케이션을 멈추는 것.

GC 실행시 GC 실행을 위한 스레드를 제외하고 모두 멈춘다.

**GC 튜닝** 은 이러한 stop the world 시간을 줄이는 것을 말한다.

**Mark and Sweep**

GC 과정을 일컫는 단어.

- Mark : GC가 스택의 모든 변수 또는 Reachable 객체를 스캔하면서 각각 어떤 객체를 참조하고 있는지 찾는 과정(이 과정에서 stop the world 발생)
- Sweep : Mark되어 있지 않는 객체들을  힙에서 제거하는 과정

**Minor GC & Major GC**

JVM의 Heap은 Young, Old, Perm 세 영역으로 나뉜다. Young영역에서 발생한 GC를 Minor GC, 나머지 두영역에서 발생한 GC를 Major GC라고 한다.

- Young
    - 새롭게 생성한 객체가 위치.
    - 구조 : eden, survivor(2개)
    - eden영역에 최초로 객체가 만들어지고, Survivor 영역을 통해서 Old 영역으로 이동
- Old
    - Young 영역에서 reachable 상태를 유지해 살아남은 객체가 복사된다. Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다.
    - GC의 종류는 JAVA의 버전이 다양해짐에 따라 GC도 다양해졌다. 여기서는 CMS GC와 G1 GC 정도만 설명을 한다.
        - CMS GC  - 속도는 빠르나, CPU를 더 많이 사용한다.

            Initial mark 단계에서 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾는 것으로 끝낸다. 따라서, 멈추는 시간이 굉장히 짧다.

            Concurrent Mark 단계에서는 방금 살아있다고 확인한 객체에서 참조하고 있는 개체들을 따라가 확인한다.

            Reamark 단게에서는 Concurrent Mark 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인하다.

            마지막으로 Concurrent Sweep 단계에서 쓰레드를 정리하는 작업을 실행한다.

        - G1 GC

            바둑판의 각 영역에 객체를 할당하고 GC를 실행한다.

            해당 영역이 꽉 차면 다른 영역에서 객체를 할당하고 GC를 실행한다.

            즉, Young → Old 영역으로 이동하는 단계가 사라진 GC 방식

- Perm(Method Area) : 클래스와 메소드 정보와 같이 자바 언어 레벨에서는 거의 사용되지 않는 영역이다.

<br><br>

더 자세한 사항은 아래 참고 주소를 확인하면 좋다.

- 참고

  - [Java Garbage Collection](https://d2.naver.com/helloworld/1329)
