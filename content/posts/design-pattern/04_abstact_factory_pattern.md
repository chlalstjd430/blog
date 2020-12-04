---
title: "[DesignPattern] Abstract factory pattern(추상 팩토리 패턴)"
date: 2020-06-08T14:02:59+09:00
categories: ["design pattern"]
---

# Abstract factory pattern(추상 팩토리 패턴)

이번에 학습할 디자인 패턴은 **추상 팩토리 패턴** 입니다.


### 추상 팩토리 패턴이란?

위키에서는 다음과 같이 정의합니다.

> 추상 팩토리 패턴(Abstract factory pattern)은 다양한 구성 요소 별로 '객체의 집합'을 생성해야 할 때 유용하다. 이 패턴을 사용하여 상황에 알맞은 객체를 생성할 수 있다.

문장으로 이해하는 것보다 정확한 예제로 이해해봅시다!

<p align = "center">
  <img src = "https://66.media.tumblr.com/1f2295e134fb4cb64f9fbe578801ff6d/tumblr_odm7p6jSef1tb8iyko1_500.gif">
</p>

### 엘리베이터 부품 업체 변경하기

다음은 LG와 현대 업체의 모터와 문을 지원하는 클래스들의 다이어그램입니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83994649-099fac00-a992-11ea-87af-5091cb272778.png">
</p>

Motor, Door Class의 핵심 메소드는 다음과 같습니다.

~~~JAVA
class Motor{
  public void move(Direction direction){
    // 1) 이미 이동 중이면 무시한다.
    // 2) 만약 문이 열려 있으면 문을 닫는다.
    // 3) 모터를 구동해서 이동시킨다.
    // 4) 모터의 상태를 이동중으로 설정한다.
  }
}

class Door{
  public void open(){
    // 1) 이미 문이 열려있으면 무시한다.
    // 2) 문을 연다 -> 이 부분만 LG, 현대에서 달라짐
    // 3) 문의 상태를 닫힘으로 설정한다.
  }
~~~

이를 유념하고, 위의 클래스다이어그램이 구현된 코드들을 살펴 봅시다.

~~~JAVA
enum DoorStatus { CLOSED, OPENED }

abstract class Door {
    private DoorStatus doorStatus;

    public Door() {
        this.doorStatus = DoorStatus.CLOSED;
    }

    public DoorStatus getDoorStatus() {
        return doorStatus;
    }

    public void close() {
        if(doorStatus == DoorStatus.CLOSED)
            return;
        doorStatus = DoorStatus.CLOSED;
    }

    protected abstract void doClose();

    public void open() {
        if(doorStatus == DoorStatus.OPENED)
            return;
        doorStatus = DoorStatus.OPENED;
    }

    protected abstract void doOpen();
}

class LGDoor extends Door{
    @Override
    protected void doClose() {
        System.out.println("close LG Door");
    }

    @Override
    protected void doOpen() {
        System.out.println("open LG Door");
    }
}

class HyundaiDoor extends Door{
    @Override
    protected void doClose() {
        System.out.println("close Hyundai    Door");
    }

    @Override
    protected void doOpen() {
        System.out.println("open Hyundai Door");
    }
}
~~~

먼저 Door와 관련된 클래스들은 [템플릿 메소드 패턴](http://blog.cmstown.com/2020/05/02_templete_method_pattern/)이 적용된 것을 확인하실 수 있습니다.

이후 모터와 도어 클래스에 [팩토리 메서드](http://blog.cmstown.com/2020/06/03_factory_method_pattern/)를 적용해보겠습니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83996412-1377de00-a997-11ea-8401-c0a577214dc6.png">
</p>

~~~JAVA
enum Direction { UP, DOWN }
enum VendorID { LG, HYUNDAI }
class MotorFactory{
    public static Motor createMotor(VendorID vendorID){
        Motor motor = null;
        switch (vendorID){
            case LG:
                motor = new LGMotor();
                break;
            case HYUNDAI:
                motor = new HyundaiMotor();
                break;
        }

        return motor;
    }
}

class DoorFactory{
    public static Door createDoor(VendorID vendorID){
        Door door = null;
        switch (vendorID){
            case HYUNDAI:
                door = new HyundaiDoor();
                break;
            case LG:
                door = new LGDoor();
                break;
        }
        return door;
    }
}

enum MotorStatus { MOVING, STOPPED }
abstract class Motor {
    private MotorStatus motorStatus;
    private Door door;

    public Motor(){
        motorStatus = MotorStatus.STOPPED;
    }

    public MotorStatus getMotorStatus(){
        return this.motorStatus;
    }

    public void setMotorStatus(MotorStatus motorStatus){
        this.motorStatus  = motorStatus;
    }

    public void move(Direction direction){
        MotorStatus motorStatus = getMotorStatus();
        if(motorStatus == motorStatus.MOVING){
            return;
        }

        door.close();

        moveMotor(direction);
        setMotorStatus(MotorStatus.MOVING);
    }

    protected abstract void moveMotor(Direction direction);

    public void stop(){
        motorStatus = MotorStatus.STOPPED;
    }

    public void setDoor(Door door){
        this.door = door;
    }
}

class LGMotor extends  Motor{
    @Override
    protected void moveMotor(Direction direction) {
        System.out.println("move LG Motor " + direction);
    }
}

class HyundaiMotor extends Motor{
    @Override
    protected void moveMotor(Direction direction) {
        System.out.println("move Hyundai Motor" + direction);
    }
}

public class Client {
    public static void main(String[] args) {
        Door lgDoor = DoorFactory.createDoor(VendorID.LG);
        Motor lgMotor = MotorFactory.createMotor(VendorID.LG);
        lgMotor.setDoor(lgDoor);

        lgDoor.open();
        lgMotor.move(Direction.UP);
    }
}
~~~

이전에 배웠던 패턴들을 이용해보았는데, 이를 통해 부품의 교체가 수월해 보입니다. 그러나 **현재는 Door와 Motor의 부품만 필요로하는데, 그 외 여러가지 부품들이 필요한 경우** 에는 어떻게 대응해야 할까요?

예를 들어 총 8개의 부품이 추가된다면, 다음과 같이 8개의 Factory Class들을 만들어야 할 것입니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83998582-e417a000-a99b-11ea-979f-8619bad11b77.png">
</p>

이렇게 되면 부품이 생길 때마다 팩토리 클래스를 만들어야 해서 코드의 복잡도는 더 커질 것입니다. 이에 해결책으로 다음과 같은 방법을 정의하였습니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84000389-7e2d1780-a99f-11ea-904f-ec66068e4854.png">
</p>

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/83999274-5a68d200-a99d-11ea-90ba-a1f25b7871b4.png">
</p>

위 다이어그램은 부품이 아니라 제조업체 별로 팩토리를 정의하였습니다.

이를 소스코드로 보면 다음과 같습니다.

~~~JAVA
abstract class ElevatorFactory{
    public abstract Motor createMotor();
    public abstract Door createDoor();
}

class LGElevatorFactory extends ElevatorFactory{
    @Override
    public Motor createMotor() {
        return new LGMotor();
    }

    @Override
    public Door createDoor() {
        return new LGDoor();
    }
}

class HyundaiElevatorFactory extends ElevatorFactory{
    @Override
    public Motor createMotor() {
        return new HyundaiMotor();
    }

    @Override
    public Door createDoor() {
        return new HyundaiDoor();
    }
}

public class Client {
    public static void main(String[] args) {
        ElevatorFactory elevatorFactory = new LGElevatorFactory();
        Door door = elevatorFactory.createDoor();
        Motor motor = elevatorFactory.createMotor();
        motor.setDoor(door);

        door.open();
        motor.move(Direction.UP);
    }
}
~~~

부품별로 팩토리를 만드는 것이 아닌 제조사별로 팩토리 클래스를만들어 비교적 간겨한 코드가 작성되었습니다.

추가적으로 삼성 엘리베이터가 다음과 같이 추가된다면 어떻게할까요?

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84002742-dbc36300-a9a3-11ea-8edd-16dde01549b6.png">
</p>

이는 다른 코드와 마찬가지로 다음과 같이 코드를 작성할 것입니다.

~~~JAVA

class SamsungElevatorFactory extends  ElevatorFactory{
    @Override
    public Motor createMotor() {
        return new SamsungMotor();
    }

    @Override
    public Door createDoor() {
        return new SamsungDoor();
    }
}

class SamsungDoor extends Door{
    @Override
    protected void doClose() {
        System.out.println("close Samsung Door");
    }

    @Override
    protected void doOpen() {
        System.out.println("open Samsung Door");
    }
}

class SamsungMotor extends Motor{
    @Override
    protected void moveMotor(Direction direction) {
        System.out.println("moce Samsung Motor");
    }
}

public class Client {
    public static void main(String[] args) {
        ElevatorFactory elevatorFactory = new SamsungElevatorFactory();
        Door door = elevatorFactory.createDoor();
        Motor motor = elevatorFactory.createMotor();
        motor.setDoor(door);

        door.open();
        motor.move(Direction.UP);
    }
}
~~~

손 쉽게 다른 제조업체의 엘리베이터도 추가할 수 있습니다! 그러면 이제 이제까지 배웠던 디자인 패턴들을 적용하여 더 코드를 효율적이게 만들지 생각해보면 더 좋은 학습니다. 예를들어, Factory 클래스를 생성하는 부분을 팩토리 메소드 패턴을 적용하여 설계하자. 그리고 제조업체 별 팩토리는 1개만 필요하다면, 싱글톤 패턴을 적용해보면 보다 더 간결하고 명확한 다이어그램이 그려질 겁니다. 해당 코드는 오늘 다루지않겠지만 모두 시간이되면 고민해보고 적용해보시면 좋을 것 같습니다!

<br><br><br>

참고
- [JAVA 객체지향 디자인 패턴 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968480911&orderClick=JAj&OV_REFFER=http://click.linkprice.com/click.php?m=kbbook&a=A100532541&l=9999&l_cd1=0&u_id=kacg2ap746029ofs02yqe&l_cd2=0&tu=http%3A%2F%2Fwww.kyobobook.co.kr%2Fproduct%2FdetailViewKor.laf%3FmallGb%3DKOR%26ejkGb%3DKOR%26barcode%3D9788968480911%26orderClick%3DJAj)
