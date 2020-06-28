---
title: "[DesignPattern] Decorator Pattern(데코레이터 패턴)"
date: 2020-05-18T21:12:02+09:00
categories: ["디자인패턴"]
---

# DecoratorPattern(데코레이터 패턴)
### 데코레인터 패턴이란?
> 객체의 결합 을 통해 기능을 동적으로 유연하게 확장 할 수 있게 해주는 패턴

위의 한 문장으로 정확히 무엇인지 정확히 이해하기 힘듭니다, 아래의 내용을 통해 데코레이터 패턴을 살펴봅시다!

### 도로 표시 방법 조합하기(데코레이터 패턴을 모를 떄 ver)

**도로가 있을 떄 차선을 표시해야할 떄 우리는 어떻게 클래스를 만들까?**



<p align="center">
  <img src = "https://user-images.githubusercontent.com/50758600/82202927-903a0e00-993d-11ea-9ea7-8a1faa323c54.png" align = "center">
</p>

도로가 있을 떄 차선을 표시하기 위해 기본적으로 2가지 클래스를 만들어보았습니다!
- RodaDisplay Class : 기본 도로 표시 기능을 제공하는 클래스
- RoadDIsplayWithLane Class : 기본 도로 표시에 추가적으로 차선을 표시하는 클래스

이 둘을 코드로 표현하면 다음과 같습니다~

~~~java
public class Client {
    public static void main(String[] args) {
        System.out.println("첫번째 draw");
        RoadDisplay roadDisplay = new RoadDisplay();
        roadDisplay.draw();
        System.out.println("\n두번째 draw");
        RoadDisplay roadDisplayWithLane = new RoadDisplayWithLane();
        roadDisplayWithLane.draw();
    }
}

class RoadDisplay{
    public void draw(){
        System.out.println("도로 기본 표시");
    }
}

class RoadDisplayWithLane extends RoadDisplay{
    public void draw(){
        super.draw();
        drawLane();
    }
    private void drawLane(){
        System.out.println("차선 표시");
    }
}
~~~

위의 코드 중 main을 보면 "draw()"라는 메소드를 사용하여 기능을 통일 시켜주었습니다. 그래서 결과를 보면

~~~
첫번째 draw
도로 기본 표시

두번째 draw
도로 기본 표시
차선 표시
~~~

다음과 같이 출력이 됩니다. 동일한 "draw()"메소드를 수행했지만 다른 출력값이 나오는 것을 확인할 수 있습니다.



**기능만 봐서는 잘 작동하는 것 같은데,,과연 해당 코드에 문제점이 없을까요?🤔**

<p align = "center">
  <img src = "https://thumbs.gfycat.com/AltruisticDisastrousBuck-size_restricted.gif">
</p>

문제점이 있는지 확인하기 위해 다음과 같은 사항을 체크해봅시다!

1. 또 다른 추가적인 도로 표시 기능을 구현하고 싶다면 어떻게 해야 하는가?(도로 표시에 교통량도 표시하고싶다면..!?)🚖🚖🚖

2. 뿐만아니라 여러가지 추가 기능의 조합하여 제공하고 싶다면 어떻게 해야 할까요? 
예를 들어 기본 도로 표시에 차선 표시 기능과 교통량 표시 기능을 함께 제공하고 싶다면요! 🏞 + 🚖🚖🚖



위의 사항들을 다음과 같이 시도해봅시다!

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/82206241-d8a7fa80-9942-11ea-82c6-27f0ea3231fa.png">
</p>

추가적으로 RoadDisplay를 상속받는 RoadDisplayWithTraffic 클래스를 구현하는거죠!! 즉, 코드를 보면 다음과 같습니다!

~~~java
public class Client {
    public static void main(String[] args) {
        System.out.println("첫번째 draw");
        RoadDisplay roadDisplay = new RoadDisplay();
        roadDisplay.draw();

        System.out.println("\n두번째 draw");
        RoadDisplay roadDisplayWithLane = new RoadDisplayWithLane();
        roadDisplayWithLane.draw();

        System.out.println("\n세번째 draw");
        RoadDisplay roadDisplayWithTraffic = new RoadDisplayWithTraffic();
        roadDisplayWithTraffic.draw();
    }
}

class RoadDisplay{
    public void draw(){
        System.out.println("도로 기본 표시");
    }
}

class RoadDisplayWithLane extends RoadDisplay{
    public void draw(){
        super.draw();
        drawLane();
    }
    private void drawLane(){
        System.out.println("차선 표시");
    }
}

class RoadDisplayWithTraffic extends RoadDisplay{
    public void draw(){
        super.draw();
        drawTraffic();
    }
    private void drawTraffic(){
        System.out.println("교통량 표시");
    }
}
~~~

이 코드의 실행 결과도 
~~~
첫번째 draw
도로 기본 표시

두번째 draw
도로 기본 표시
차선 표시

세번째 draw
도로 기본 표시
교통량 표시
~~~
로 잘 수행이됩니다. 그렇다면 이와 같은 방식으로 **기능들을 조합하여 사용하자는 두번째 사항** 에 부합되도록 하려면 어떻게할까요?

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/82206990-1e18f780-9944-11ea-91c2-95a2057efbcb.png">
</p>

위와 같은 표처럼 많은 기능을 구현하기 위해 우리는 다음과 같이 코드를 작성해야할지 모릅니다..

~~~java
class RoadDisplay{
    public void draw(){
        System.out.println("도로 기본 표시");
    }
}

class RoadDisplayWithLane extends RoadDisplay{
    public void draw(){
        super.draw();
        drawLane();
    }
    private void drawLane(){
        System.out.println("차선 표시");
    }
}

class RoadDisplayWithTraffic extends RoadDisplay{
    public void draw(){
        super.draw();
        drawTraffic();
    }
    private void drawTraffic(){
        System.out.println("교통량 표시");
    }
}

class RoadDisplayWithCrossing extends RoadDisplay{
    public void draw(){
        super.draw();
        drawCrossing();
    }
    private void drawCrossing(){
        System.out.println("교차료 표시");
    }
}

class RoadDisplayWithLaneTraffic extends RoadDisplay{
    public void draw(){
        super.draw();
        drawLane();
        drawTraffic();
    }
    private void drawLane(){
        System.out.println("차선 표시");
    }
    private void drawTraffic(){
        System.out.println("교통량 표시");
    }
}

class RoadDisplayWithLaneCrossing extends RoadDisplay{
    public void draw(){
        super.draw();
        drawLane();
        drawCrossing();
    }
    private void drawLane(){
        System.out.println("차선 표시");
    }
    private void drawCrossing(){
        System.out.println("교차료 표시");
    }
}
                  .
                  .
                  .
                  .
                  .
~~~

위의 코드를 보면 무언가 문제가 있다고 느껴지시지 않나요? 위의 코드를 보면 중복된 코드들이 많이 보입니다. "차선 표시"를 print해주는 문장만 벌써 3개 등장했습니다..
그러면 기능들이 추가될 떄마다 중복된 코드는....

<p align = "center">
  <img src = "https://media.tenor.com/images/ea8bc435841d346bfad9d47090b8eeff/tenor.gif">
  <img src = "https://media.tenor.com/images/ea8bc435841d346bfad9d47090b8eeff/tenor.gif">
  <img src = "https://media.tenor.com/images/ea8bc435841d346bfad9d47090b8eeff/tenor.gif">
</p>

맞습니다..기능이 추가될 떄마다 우리는 불필요한 하드 코딩을 하게 됩니다.


그러면 데코레이터 패턴을 적용하면 어떻게 해결될까요!?

### 데코레이터 패턴 적용 ver

다음은 데코레이터 패턴을 적용한 후의 클래스 다이어그램입니다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/82208863-40604480-9947-11ea-8d6a-50d8b32a6688.png">
</p>

훨씬 간결해진 구조 보이시나요!? 기존에 기능들을 조합하기 위해 조합되는 기능만큼 클래스들을 생성했는데, 데코리에터 패턴을 이용하면 고유한 기능들 개수만큼만 클래스를 생성해주시면 됩니다!
그러면 코드를 볼까요?

~~~java
public class Client {
    public static void main(String[] args) {
        Display roadWithLaneAndTraffic = new TrafficDecorator(
                new LaneDecorator(
                        new CrossingDecorator(
                                new RoadDisplay()
                        )
                )
        );
        System.out.println("--- draw 호출 ---");
        roadWithLaneAndTraffic.draw();
    }
}
abstract class Display{
    public abstract void draw();
}

class RoadDisplay extends Display{
    @Override
    public void draw() {
        System.out.println("도로 기본 표시");
    }
}

class DisplayDecorator extends Display{
    private Display decorateDisplay;

    public DisplayDecorator(Display decorateDisplay){
        this.decorateDisplay = decorateDisplay;
    }

    @Override
    public void draw() {
        decorateDisplay.draw();
    }
}

class LaneDecorator extends DisplayDecorator{
    public LaneDecorator(Display decorateDisplay){
        super(decorateDisplay);
    }

    @Override
    public void draw() {
        super.draw();
        drawLane();
    }

    private void drawLane(){
        System.out.println("\t차선 표시");
    }
}

class CrossingDecorator extends DisplayDecorator{
    public CrossingDecorator(Display decorateDisplay){
        super(decorateDisplay);
    }

    @Override
    public void draw() {
        super.draw();
        drawCrossing();
    }

    private void drawCrossing(){
        System.out.println("\t횡단보도 표시");
    }
}

class TrafficDecorator extends DisplayDecorator{
    public TrafficDecorator(Display decorateDisplay){
        super(decorateDisplay);
    }

    @Override
    public void draw() {
        super.draw();
        drawTraffic();
    }

    private void drawTraffic(){
        System.out.println("\t교통량 표시");
    }
}
~~~

수행결과
~~~
--- draw 호출 ---
도로 기본 표시
	횡단보도 표시
	차선 표시
	교통량 표시
~~~

main안의 코드들을 살펴봅시다! 예전에 해당 기능을 구현하기 위해서는 새로 클래스를 만들어서 구현했는데, 데코레이터 페턴이 적용된 현재는
생성자 DisplayDecorator 클래스를 상속받는 클래스들의 생성자에 구현하고자 하는 기능들을 추가하면 간단하게 구현됩니다. 즉, 새로운 클래스 구현이 아닌 생성자에 필요한 기능을 담는 것으로 기능의 조합이 가능해집니다! 

이로써 우리는 앞으로 데코레이터 패턴을 이용하여 기본 기능에 추가될 수 있는 많은 수의 부가 기능에 대해서 다양한 조합을 동적으로 구현할 수 있습니다~!
<p align = "center">
  <img src="https://media0.giphy.com/media/vcKEsYOdjoCeJRpn95/giphy.gif">
</p>



참고
- [JAVA 객체지향 디자인 패턴 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968480911&orderClick=JAj&OV_REFFER=http://click.linkprice.com/click.php?m=kbbook&a=A100532541&l=9999&l_cd1=0&u_id=kacg2ap746029ofs02yqe&l_cd2=0&tu=http%3A%2F%2Fwww.kyobobook.co.kr%2Fproduct%2FdetailViewKor.laf%3FmallGb%3DKOR%26ejkGb%3DKOR%26barcode%3D9788968480911%26orderClick%3DJAj)
