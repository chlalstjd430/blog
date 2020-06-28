---
title: "[DesignPattern] Factory method pattern(팩토리 메소드 패턴)"
date: 2020-06-02T02:04:49+09:00
categories: ["디자인패턴"]
---

# Factory method pattern(팩토리 메소드 패턴)

이번에 학습할 디자인 패턴은 "팩토리 메소드 패턴" 입니다.

### 팩토리 메소드 메서드 패턴이란?

위키에서는 다음과 같이 설명합니다.

 > Factory method는 부모(상위) 클래스에 알려지지 않은 구체 클래스를 생성하는 패턴이며. 자식(하위) 클래스가 어떤 객체를 생성할지를 결정하도록 하는 패턴이기도 하다. 부모(상위) 클래스 코드에 구체 클래스 이름을 감추기 위한 방법으로도 사용한다.

이번에도 역시나 예제로 자세히 살펴보도록 합시다.

 <p align = "center">
   <img src = "https://media1.tenor.com/images/e8144164703c22c5d7b46fd1e44f82ae/tenor.gif?itemid=5333229">
 </p>

<br><br>

### 여러 방식의 엘리베이터 스케줄링 방법 지원하기(일반)

엘리베이터 제어 시스템에서 다양한 엘리베이터 스케줄링을 지원한다고 했을 때, 다음과 같은 구조의 클래스가 있다고 가정해봅시다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83435194-c071c880-a476-11ea-83a7-dcd94be8f9e6.png">
</p>

- ElevatorManager Class : 여러 엘리베이터 중에서 스케줄링에 따라서 하나의 엘리베이터를 선택하고 이동시킴

- EleveatorController Class : 하나의 엘리베이터 이동을 제어하는 클래스

- ThroughputScheduler Class : 처리량을 기준으로 스케줄링하는 클래스



코드로 표현하면, 다음과 같습니다.

~~~JAVA

enum Direction {UP, DOW}

class ElevatorManager {
    private List<ElevatorController> controllers;
    private ThroughputScheduler scheduler;

    public ElevatorManager(int controllerCount){
        controllers = new ArrayList<ElevatorController>(controllerCount);
        for(int i = 0; i < controllerCount; i++){
            ElevatorController controller = new ElevatorController(i);
            controllers.add(controller);
        }
        scheduler = new ThroughputScheduler();
    }

    public void requestElevator(int destination, Direction direction){
        int selectedElevator = scheduler.selectElevator(this, destination, direction);
        controllers.get(selectedElevator).gotoFloor(destination);
    }
}

class ElevatorController {
    private int id;
    private int curFloor;

    public ElevatorController(int id) {
        this.id = id;
        curFloor = 1;
    }

    public void gotoFloor(int destination){
        System.out.println("Elevator [" + id + "] Floor: " + curFloor);
        curFloor = destination;
        System.out.println(" ==> " + curFloor);
    }
}

class ThroughputScheduler {
    public int selectElevator(ElevatorManager manager, int destination, Direction direction){
      // 실제 구현하지는 않음
        return 0;
    }
}


public class Client {
    public static void main(String[] args) {
        ElevatorManager em = new ElevatorManager(1);
        em.requestElevator(4, Direction.UP);
    }
}

~~~

먼저 코도를 잘 읽어가며 각 클래스들의 기능들을 자세히 파악해봅시다. 이후 다음과 문제점을 어떻게 해결할지 생각해봅시다.

- **🙋‍♂️문제점**

  - 현대 ElevatorManager는 ThroughputScheduler를 이용하고 있다. 즉 엘리베이터의 처리량을 최대화시키는 전략을 사용하고 있다. 만약 다른 스케쥴링 전략을 사용해야 한다면? 예를 들어 사용자의 대기시간을 최소화시키는 엘리베이터를 선택하는 전략을 사용해야 한다면?

  - 스케쥴링 전략이 프로그램 실행 중에 변경을 해야 한다면? 즉, 동적 스케쥴링을 지원해야 한다면 어떻게 해야할까? 예를 들어 오전에는 대기시간 최소화 전략을 사용하고 오후에는 처리량 최대화 전략을 사용해야 한다면?

먼저 기존에 자주 사용하는 패턴으로 "Strategy Patter(전략 패턴)" 을 생각하기 쉽습니다. 그렇다면 해당 패턴으로 위와 같은 문제를 해결해봅시다!

<br><br><br>

### 여러 방식의 엘리베이터 스케줄링 방법 지원하기(Strategy patter 적용)

스트래티지 패턴을 적용하면, 다음과 같이 클래스 구조가 나오게 됩니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83437904-8525c880-a47b-11ea-922a-0baa98c00ddb.png">
</p>

위의 다이어그램에서 ElevatorController가 표현되어있지 않지만, ElevatorManager와 연결되어있다고 생각하고 보면 된다.

해당 구조를 보면 ElevatorSheduler를 구현한 클래스들이 생겨서, 위의 문제점을 해결할 수 있을 것으로 보입니다. 실제 코드를 봐볼까요?

~~~JAVA
interface ElevatorScheduler {
    public int selectElevator(ElevatorManager manager, int destination, Direction direction);
}

class ThroughputScheduler implements ElevatorScheduler{
    @Override
    public int selectElevator(ElevatorManager manager, int destination, Direction direction){
        // 실제 구현하지는 않음
        return 0;
    }
}

class ResponseTimeScheduler implements ElevatorScheduler {
    @Override
    public int selectElevator(ElevatorManager manager, int destination, Direction direction) {
        // 실제 구현하지는 않음
        return 1;
    }
}

enum Direction {UP, DOW}

class ElevatorManager {
    private List<ElevatorController> controllers;

    public ElevatorManager(int controllerCount){
        controllers = new ArrayList<ElevatorController>(controllerCount);
        for(int i = 0; i < controllerCount; i++){
            ElevatorController controller = new ElevatorController(i);
            controllers.add(controller);
        }
    }

    public void requestElevator(int destination, Direction direction){
        ElevatorScheduler scheduler;

        int hour = Calendar.getInstance().get(Calendar.HOUR_OF_DAY);
        if(hour < 12) {
            scheduler = new ResponseTimeScheduler();
        }
        else{
            scheduler = new ThroughputScheduler();
        }

        int selectedElevator = scheduler.selectElevator(this, destination, direction);
        controllers.get(selectedElevator).gotoFloor(destination);
    }
}
~~~

확실히 이전 문제점을 해결한 것 같습니다. 그러나 ElevatorManager의 requestElevator method를 자세히 살펴보도록 하겠습니다. requestElevator()는 이미 선택된 전략을가지고 엘리베이터를 이동시키는 것이 해당 메소드의 책임입니다. 그런데 현재 코드에서는 전략까지 해당 메소드에서 선택하기 때문에 [함수 설계 원칙](http://blog.cmstown.com/2020/01/%ED%81%B4%EB%A6%B0%EC%BD%94%EB%93%9C_003/)에 어긋나게 됩니다.

~~~JAVA
int hour = Calendar.getInstance().get(Calendar.HOUR_OF_DAY);
if(hour < 12) {
    scheduler = new ResponseTimeScheduler();
}
else{
    scheduler = new ThroughputScheduler();
}
~~~
위의 코드는 requestElevator에 있으면 옳은 방법은 아닙니다. 예를들어 현재 오전/오후에 나누어져 스케쥴러를 다르게 하지만, 특별한 공휴일, 평일, 주말마다 다르게 구현하기 위해서는 현재의 코드로는 표현하기 무리가 있어 보입니다. 그렇기에 **전략을 선택하는 기능** 을 분리하는게 좋아보입니다.


<br><br><br>

### 여러 방식의 엘리베이터 스케줄링 방법 지원하기(Factory method pattern 적용)

팩토리 메소드 패턴을 이용하여 스케줄링 전략에 맞는 객체를 생성하는 코드를 별도로 정의하여봅시다. 그러면 다음과 같은 구조가 나오게 됩니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83440223-81944080-a47f-11ea-9819-815939d1aaec.png">
</p>

기존의 클래스들에 SchedulerFactory가 추가된 것을 확인하실 수 있습니다. 해당 클래스에서는 전략들을 생성하는 알고리즘에 의해서 만들어진 Schedular를 반환하게끔 합니다. 즉, 스케줄러를 선택하는 행위는 SchedulerFactory가 하게 됩니다.

이를 실제 코드로 살펴보도록 하겠습니다!

~~~JAVA
enum Direction {UP, DOWN}

class ElevatorManager {
    private List<ElevatorController> controllers;
    private SchedulingStrategyID strategyID;

    public ElevatorManager(int controllerCount, SchedulingStrategyID strategyID){
        controllers = new ArrayList<ElevatorController>(controllerCount);
        for(int i = 0; i < controllerCount; i++){
            ElevatorController controller = new ElevatorController(i);
            controllers.add(controller);
        }
        this.strategyID = strategyID;
    }

    public void setStrategyID(SchedulingStrategyID strategyID){
        this.strategyID = strategyID;
    }

    public void requestElevator(int destination, Direction direction){
        ElevatorScheduler scheduler = SchedulerFactory.getScheduler(strategyID);
        System.out.println(scheduler);
        int selectedElevator = scheduler.selectElevator(this, destination, direction);
        controllers.get(selectedElevator).gotoFloor(destination);
    }
}

class ElevatorController {
    private int id;
    private int curFloor;

    public ElevatorController(int id) {
        this.id = id;
        curFloor = 1;
    }

    public void gotoFloor(int destination){
        System.out.println("Elevator [" + id + "] Floor: " + curFloor);
        curFloor = destination;
        System.out.println(" ==> " + curFloor);
    }
}

enum SchedulingStrategyID {RESPONSE_TIME, THROUGHPUT, DYNAMIC}

class SchedulerFactory {
    public static ElevatorScheduler getScheduler(SchedulingStrategyID strategyID){
        ElevatorScheduler scheduler = null;
        switch (strategyID) {
            case RESPONSE_TIME:
                scheduler = new ResponseTimeScheduler();
                break;
            case THROUGHPUT:
                scheduler = new ThroughputScheduler();
                break;
            case DYNAMIC: {
                int hour = Calendar.getInstance().get(Calendar.HOUR_OF_DAY);
                if(hour < 12) {
                    scheduler = new ResponseTimeScheduler();
                }
                else{
                    scheduler = new ThroughputScheduler();
                }
                break;
            }
        }
        return scheduler;
    }
}

interface ElevatorScheduler {
    public int selectElevator(ElevatorManager manager, int destination, Direction direction);
}

class ThroughputScheduler implements ElevatorScheduler{
    @Override
    public int selectElevator(ElevatorManager manager, int destination, Direction direction){
        // 실제 구현하지는 않음
        return 0;
    }
}

class ResponseTimeScheduler implements ElevatorScheduler {
    @Override
    public int selectElevator(ElevatorManager manager, int destination, Direction direction) {
        // 실제 구현하지는 않음
        return 1;
    }
}

public class Client {
    public static void main(String[] args) {
        ElevatorManager emWithResponseTimerScheduler = new ElevatorManager(2, SchedulingStrategyID.RESPONSE_TIME);
        emWithResponseTimerScheduler.requestElevator(10, Direction.UP);

        ElevatorManager emWithThroughputScheduler = new ElevatorManager(2, SchedulingStrategyID.RESPONSE_TIME);
        emWithThroughputScheduler.requestElevator(10, Direction.UP);

        ElevatorManager emWithDynamicScheduler = new ElevatorManager(2, SchedulingStrategyID.RESPONSE_TIME);
        emWithDynamicScheduler.requestElevator(10, Direction.UP);
    }
}
~~~

SchedulerFactory Class를 보면 확실히 이전과 다르게 스케줄러를 선택하는 책임이 분리가 되는 것을 확인하실 수 있습니다.

추가적으로 스케줄러는 지속적으로 사용되기 때문에 각각의 스케줄러를 싱글톤 패턴까지 적용하면 더 성능적으로 효율적인 구조가 될 수 있습니다. 그러나 오늘 이 시간에서는 팩토리 메소드 패턴을 설명하기 위한 시간이기 때문에, 생략하고 이 부분은 스스로 고민하시고 적용해보시면 좋을 것 같습니다:D

더불어 팩토리 메소드 패턴은 상속을 이용하여 구현을 할 수도 있는데 간단하게만 설명하도록 하겠습니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83443900-7d6b2180-a485-11ea-8f85-ccb38b84aac2.png">
</p>

디자인 패턴을 구현하는데 절대적인 방법은 없습니다. 디자인 패턴은 다양한 방법으로 적용할 수 있기에 예제에 나온 방법만 생각하시지마시고 원론적인 부분을 이해하여 필요할 때 실제 프로젝트에 적용하며 학습하시면 좋을 것 같습니다.

<br><br><br>

결과적으로 위의 과정을 통해서 Factory method pattern을 이용하면 객체의 생성 코드를 별도의 클래스/메소드로 분리함으로써 객체 생성의 변화를 대비하는데 굉장히 유용한 것을 알 수 있었습니다.


<br><br><br>

참고
- [JAVA 객체지향 디자인 패턴 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968480911&orderClick=JAj&OV_REFFER=http://click.linkprice.com/click.php?m=kbbook&a=A100532541&l=9999&l_cd1=0&u_id=kacg2ap746029ofs02yqe&l_cd2=0&tu=http%3A%2F%2Fwww.kyobobook.co.kr%2Fproduct%2FdetailViewKor.laf%3FmallGb%3DKOR%26ejkGb%3DKOR%26barcode%3D9788968480911%26orderClick%3DJAj)
