---
title: "[DesignPattern] Template Method Pattern(템플릿 메소드 패턴)"
date: 2020-05-26T15:37:48+09:00
categories: ["design pattern"]
---

# Template Method Pattern(템플릿 메소드 패턴)

이번에 학습할 디자인 패턴은 "템플릿 메소드 패턴" 입니다.

### 템플릿 메서드 패턴이란?

> 알고리즘의 구조를 변경하지 않고 알고리즘의 특정 단계들을 다시 정의할 수 있게 해준다.

위의 내용은 위키에 나온 정의입니다. 해당 문장을 다시 표현하자면 "구현시 달라질 수 있는 메소드들을 구현클래스에서 선언 후 호출"하는 디자인 패턴입니다.



백문이 불여일견, 같이 예제를 가지고 살펴보도록 합시다.

<p align = "center">
  <img src = "https://media2.giphy.com/media/VGbsjSuQAnj0rWFF4o/source.gif" width = "30%" height = "30%">
</p>


### 엘레베이터 제어 시스템에서 모터를 구동시키는 기능(일반 ver)

**엘레베이터 제어 시스템에서 도터를 구동시킬 떄 여러 회사의 모터를 지원하기 위해서는 어떻게 할까?**


<br>

먼저 다음과 같은 구조의 클래스가 있다고 생각해봅시다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/82870394-d2c99f00-9f6a-11ea-8da2-1baf20b9689a.png">
  <img src = "https://user-images.githubusercontent.com/50758600/82870786-7b77fe80-9f6b-11ea-86cc-3b2b6bfd360c.png">
</p>

- HyundiaMotor Class : 모터를 제어하여 엘리베이터를 이동시키는 클래스가
- Door Class : 문을 열거나 다는 기능을 제공하는 클래스

이를 코드로 표현하면 다음과 같습니다

~~~JAVA
enum DoorStatus { CLOSED, OPENED }
enum MotorStatus { MOVING, STOPPED }
enum Direction { UP, DOWN }

class Door {
    private DoorStatus doorStatus;

    public Door() {
        this.doorStatus = DoorStatus.CLOSED;
    }

    public DoorStatus getDoorStatus() {
        return doorStatus;
    }

    public void close() {
        doorStatus = DoorStatus.CLOSED;
    }

    public void open() {
        doorStatus = DoorStatus.OPENED;
    }
}

class HyundaiMotor {
    private Door door;
    private MotorStatus motorStatus;

    public HyundaiMotor(Door door) {
        this.door = door;
        this.motorStatus = MotorStatus.STOPPED;
    }

    private void moveHyundaiMotor(Direction direction){
        System.out.println("Hyundai Motor 구동");
    }

    public MotorStatus getMotorStatus() {
        return motorStatus;
    }

    private void setMotorStatus(MotorStatus motorStatus){
        this.motorStatus = motorStatus;
    }

    public void move(Direction direction){
        MotorStatus motorStatus = getMotorStatus();
        if(motorStatus == MotorStatus.MOVING)
            return;

        DoorStatus doorStatus = door.getDoorStatus();
        if(doorStatus == DoorStatus.OPENED)
            door.close();

        moveHyundaiMotor(direction);
        setMotorStatus(MotorStatus.MOVING);
    }
}
~~~

위와 같은 코드를 가지고 실제 현대모터를 가지고 윗층으로 이동시키는 코드를 작성하면

~~~JAVA
public class Client {
    public static void main(String[] args) {
        Door door = new Door();
        HyundaiMotor hyundaiMotor = new HyundaiMotor(door);
        hyundaiMotor.move(Direction.UP);
    }
}
~~~

다음과 같이 구현할 수 있습니다.

지금까지 현대 모터를 가지고 실행되는 엘리베이터를 만들었는데, 어떠한 상황이 생겨서 LG 모터를 사용해야 한다고 생각해봅시다. 어떻게 해야할까요?

먼저 LG Moter라는 클래스를 다음과 같이 구현할 것입니다.

~~~JAVA
class LGMoter {
    private Door door;
    private MotorStatus motorStatus;

    public LGMoter(Door door) {
        this.door = door;
        this.motorStatus = MotorStatus.STOPPED;
    }

    private void moveLGMoter(Direction direction){
        System.out.println("LG Motor 구동");
    }

    public MotorStatus getMotorStatus() {
        return motorStatus;
    }

    private void setMotorStatus(MotorStatus motorStatus){
        this.motorStatus = motorStatus;
    }

    public void move(Direction direction){
        MotorStatus motorStatus = getMotorStatus();
        if(motorStatus == MotorStatus.MOVING)
            return;

        DoorStatus doorStatus = door.getDoorStatus();
        if(doorStatus == DoorStatus.OPENED)
            door.close();

        moveLGMoter(direction);
        setMotorStatus(MotorStatus.MOVING);
    }
}
~~~
이렇게 구현하면 LGMoter도 마찬가지로 사용가능하게 될겁니다. 그러나 코드를 보면 MoveLGMoter 메소드말고는 바뀐 부분이 없습니다. 이는 OCP 원칙에 위배됩니다. 그래서 다음과 같이 클래스 구조를 변경할 것입니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/82874530-0a3b4a00-9f71-11ea-9268-f8182032d461.png">
</p>

이렇게 하면 OCP원칙을 지키며 모터를 상속만해주면 다양한 모터들을 구현할 수 있는 것으로 보입니다. 코드로 구현하면 다음과 같습니다.

~~~JAVA
abstract class Motor {
    protected Door door;
    private MotorStatus motorStatus;

    public Motor(Door door) {
        this.door = door;
        motorStatus = MotorStatus.STOPPED;
    }

    public MotorStatus getMotorStatus() {
        return motorStatus;
    }

    protected void setMotorStatus(MotorStatus motorStatus) {
        this.motorStatus = motorStatus;
    }
}

class HyundaiMotor extends Motor {

    public HyundaiMotor(Door door) {
        super(door);
    }

    private void moveHyundaiMotor(Direction direction){
        System.out.println("Hyundai Motor 구동");
    }

    public void move(Direction direction){
        MotorStatus motorStatus = getMotorStatus();
        if(motorStatus == MotorStatus.MOVING)
            return;

        DoorStatus doorStatus = door.getDoorStatus();
        if(doorStatus == DoorStatus.OPENED)
            door.close();

        moveHyundaiMotor(direction);
        setMotorStatus(MotorStatus.MOVING);
    }
}

class LGMoter extends Motor {
    public LGMoter(Door door) {
        super(door);
    }

    private void moveLGMoter(Direction direction){
        System.out.println("LG Motor 구동");
    }

    public void move(Direction direction){
        MotorStatus motorStatus = getMotorStatus();
        if(motorStatus == MotorStatus.MOVING)
            return;

        DoorStatus doorStatus = door.getDoorStatus();
        if(doorStatus == DoorStatus.OPENED)
            door.close();

        moveLGMoter(direction);
        setMotorStatus(MotorStatus.MOVING);
    }
}
~~~

비교적 깔끔해진 코드를 볼 수있습니다. 그러나 자세히 move 메소드를 살펴보면 현대와 LG 모터 모두 똑같은 코드가 중복되는 부분이 보입니다. 지금 2개의 모터를 예제로 들었지만 이후 10개,100개의 모터와 호환이 되어야한다면 수 많은 코드가 중복되어 해당 로직이 변경된다고 하면 10번, 100번 넘게 코드를 수정해야하는 높은 유지보수 비용이 발생합니다. 그렇다면 어떻게 이를 대처할까요?


### 엘레베이터 제어 시스템에서 모터를 구동시키는 기능(템플릿 메소드 패턴 적용 ver)

이러한 상황에서 템플릿 메소드 패턴을 활용하는 것을 살펴보도록 하겠습니다. 패턴을 적용하면 다음과 같은 구조가 나오게 됩니다.

<p aling = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/82915410-a97d3300-9fab-11ea-9c8f-134b98796bdf.png">
</p>

얼핏 보면 크게 달라진게 없어보이지만, Motor 추상 클래스에 move와 moveMotor 메소드가 추가된것을 확인하실 수 있습니다. 이를 코드를 통해 자세히 확인해봅시다.

~~~JAVA
abstract class Motor {
    private Door door;
    private MotorStatus motorStatus;

    public Motor(Door door) {
        this.door = door;
        motorStatus = MotorStatus.STOPPED;
    }

    public MotorStatus getMotorStatus() {
        return motorStatus;
    }

    private void setMotorStatus(MotorStatus motorStatus) {
        this.motorStatus = motorStatus;
    }

    public void move(Direction direction){
        MotorStatus motorStatus = getMotorStatus();
        if(motorStatus == MotorStatus.MOVING)
            return;

        DoorStatus doorStatus = door.getDoorStatus();
        if(doorStatus == DoorStatus.OPENED)
            door.close();

        moveMotor(direction);
        setMotorStatus(MotorStatus.MOVING);
    }

    protected abstract void moveMotor(Direction direction);
}

class HyundaiMotor extends Motor {

    public HyundaiMotor(Door door) {
        super(door);
    }

    public void moveMotor(Direction direction){
        System.out.println("Hyundai Motor 구동");
    }


}

class LGMotor extends Motor {
    public LGMotor(Door door) {
        super(door);
    }

    public void moveMotor(Direction direction){
        System.out.println("LG Motor 구동");
    }
}
~~~

Motor 추상 클래스에 move메소드를 구현하고 moveMotor 추상 메소드를 활용하여 HyudaMotor와 LGMotor가 훨씬 간소해졌습니다. 뿐만 아니라 전체적인 코드량도 감소하였고, move의 로직이 변경될 떄 Motor의 move 메소드만 변경하면되서 유지보스 측면에서도 훨씬 좋아졌습니다.

위와 같은 상황을 보았을 떄, 템플릿 메소드 패턴을 활용하면 전체적인 알고리즘을 구현하면서 상이한 부분은 하위 클래스에서 구현할 수 있도록 해 주는 디자인 패턴으로서 전체적인 알고리즘의 코드를 재사용하는데 유용하다는 것을 알 수 있었습니다.


참고
- [JAVA 객체지향 디자인 패턴 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968480911&orderClick=JAj&OV_REFFER=http://click.linkprice.com/click.php?m=kbbook&a=A100532541&l=9999&l_cd1=0&u_id=kacg2ap746029ofs02yqe&l_cd2=0&tu=http%3A%2F%2Fwww.kyobobook.co.kr%2Fproduct%2FdetailViewKor.laf%3FmallGb%3DKOR%26ejkGb%3DKOR%26barcode%3D9788968480911%26orderClick%3DJAj)
