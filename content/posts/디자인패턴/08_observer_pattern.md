---
title: "[DesignPattern] Observer pattern(옵저버 패턴)"
date: 2020-06-13T23:13:36+09:00
categories: ["디자인패턴"]
---

#  Observer pattern(옵저버 패턴)

이번에 학습할 디자인 패턴은 **옵저버 패턴** 입니다.


### Observer pattern(옵저버 패턴)이란?


위키에서는 다음과 같이 정의합니다.

> 옵서버 패턴(observer pattern)은 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴이다. 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용된다. 발행/구독 모델로 알려져 있기도 하다.


예제와 함께 커맨드 패턴에 대해 자세히 알아보도록 하겠습니다.

### 여러가지 방식으로 성적 출력하기

성적을 출력해주는 클래스가 다음과 같이 있다고 하자.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84571981-fe091680-add1-11ea-9ad1-eb6d0065636b.png"/>
</p>

- ScoreRecord 클래스: 점수를 저장/관리하는 클래스

- DataSheetView 클래스: 점수를 목록형태로 출력하는 클래스

코드로 구현하면 다음과 같다.

~~~JAVA
public class Client {
    public static void main(String[] args) {
        ScoreRecord scoreRecord = new ScoreRecord();
        DataSheetView dataSheetView = new DataSheetView(scoreRecord, 3);

        scoreRecord.setDataSheetView(dataSheetView);

        for(int i = 1; i <= 5; i++){
            int score = i * 10;
            System.out.println("Adding " + score);

            scoreRecord.addScore(score);
        }
    }

}

class ScoreRecord {
    private List<Integer> scores = new ArrayList<>();
    private DataSheetView dataSheetView;

    public void setDataSheetView(DataSheetView dataSheetView){
        this.dataSheetView = dataSheetView;
    }

    public void addScore(int score){
        this.scores.add(score);
        dataSheetView.update();
    }

    public List<Integer> getScoredRecord(){
        return scores;
    }
}

class DataSheetView {
    private ScoreRecord scoreRecord;
    private int viewCount;

    public DataSheetView(ScoreRecord scoreRecord, int viewCount) {
        this.scoreRecord = scoreRecord;
        this.viewCount = viewCount;
    }

    public void update(){
        List<Integer> record = scoreRecord.getScoredRecord();
        displayScore(record, viewCount);
    }

    public void displayScore(List<Integer> record, int viewCount){
        System.out.print("List of " + viewCount + " entries: ");
        for (int i = 0; i < viewCount && i < record.size(); i++){
            System.out.println(record.get(i) + " ");
        }
        System.out.println();
    }
}
~~~

해당 코드를 보고 다음과 같은 문제에 대해 고민해보자.

1. 성적을 다른 방식으로 출력하고 싶다면 어떤 변경작업을 해야 하는가? 에를 들어 성적을 목록으로 출력하지 않고 최소/최대값만을 출력하려면?

2. 성적을 동시에 여러 가지 형태로 출력하려면 어떤 변경 작업을 해야 하는가?

3. 프로그램이 실행 시에 성적의 출력 대상이 변경되는 것을 지원한다면 어떤 변경 작업을 해야 하는가?

현재 코드에서 다음과 같은 작업을 할 경우, 뷰가 추가될때마다 ScoreRecord에 추가해줘야 하기 때문에 ScoreRecord가 지나치게 비대해지고, 이외에도 코드의 중복도 발생할 것이다.

이에 성적 통보 대상이 변경되더라도 ScoreRecord 클래스를 그대로 재사용할 수 있도록 옵저버 패턴을 이용하여 클래스 구조를 바꿔보자.

### 여러가지 방식으로 성적 출력하기(옵저버 패턴)

먼저 옵저버 패턴은

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84573929-6958e580-adde-11ea-9156-aa71c80e9d18.png"/>
</p>

위와 같은 클래스 구조로 이루어져야 한다.

옵저버 패턴은 통보 대상 객체의 관리를 Subject 클래스와 Observer 인터페이스로 일반화한다. 그러면 데이터 변경을 통보하는 클래스(ConcreteSubject)는 통보 대상 클래스/객체ConcreteObserver)에 대한 **의존성을 제거** 할 수 있다. 결과적으로 옵저버 패턴은 통보 대상 클래스나 대상 객체의 변경에도 ConcreteSubject 클래스를 수정 없이 그대로 사용할 수 있도록 한다.

<p align = "center">
  <img src = "https://user-images.githubusercontent.com/50758600/84572680-af11b000-add6-11ea-8351-8cdd15ca61a3.png"/>
</p>

옵저버 패턱을 적용하면 클래스 구조는 위와 같아집니다.

그리고 코드는 다음과 같습니다.

~~~JAVA

interface Observer {
    abstract public void update();
}

abstract class Subject {
    private List<Observer> observers = new ArrayList<>();

    public void attach(Observer observer){
        observers.add(observer);
    }

    public void detach(Observer observer){
        observers.remove(observer);
    }

    public void notifyObservers(){
        for (Observer o : observers) o.update();
    }
}

class ScoreRecord extends Subject {
    private List<Integer> scores = new ArrayList<>();

    public void addScore(int score){
        this.scores.add(score);
        notifyObservers();
    }

    public List<Integer> getScoredRecord(){
        return scores;
    }
}

class DataSheetView implements Observer {
    private ScoreRecord scoreRecord;
    private int viewCount;

    public DataSheetView(ScoreRecord scoreRecord, int viewCount) {
        this.scoreRecord = scoreRecord;
        this.viewCount = viewCount;
    }

    public void update(){
        List<Integer> record = scoreRecord.getScoredRecord();
        displayScore(record, viewCount);
    }

    public void displayScore(List<Integer> record, int viewCount){
        System.out.print("List of " + viewCount + " entries: ");
        for (int i = 0; i < viewCount && i < record.size(); i++){
            System.out.println(record.get(i) + " ");
        }
        System.out.println();
    }
}

class MinMaxView implements Observer {
    private ScoreRecord scoreRecord;

    public MinMaxView(ScoreRecord scoreRecord){
        this.scoreRecord = scoreRecord;
    }

    @Override
    public void update() {
        List<Integer> record = scoreRecord.getScoredRecord();
        displayMinMax(record);
    }

    private void displayMinMax(List<Integer> record){
        int min = Collections.min(record, null);
        int max = Collections.max(record, null);
        System.out.println("Min: " + min + " Max: " + max);
    }
}


public class Client {
    public static void main(String[] args) {
        ScoreRecord scoreRecord = new ScoreRecord();
        DataSheetView dataSheetView3 = new DataSheetView(scoreRecord, 3);
        DataSheetView dataSheetView5 = new DataSheetView(scoreRecord, 5);
        MinMaxView minMaxView = new MinMaxView(scoreRecord);

        scoreRecord.attach(dataSheetView3);
        scoreRecord.attach(dataSheetView5);
        scoreRecord.attach(minMaxView);

        for(int i = 1; i <= 5; i++){
            int score = i * 10;
            System.out.println("Adding " + score);
            scoreRecord.addScore(score);
        }
    }
}
~~~



<br><br><br>

참고
- [JAVA 객체지향 디자인 패턴 ](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788968480911&orderClick=JAj&OV_REFFER=http://click.linkprice.com/click.php?m=kbbook&a=A100532541&l=9999&l_cd1=0&u_id=kacg2ap746029ofs02yqe&l_cd2=0&tu=http%3A%2F%2Fwww.kyobobook.co.kr%2Fproduct%2FdetailViewKor.laf%3FmallGb%3DKOR%26ejkGb%3DKOR%26barcode%3D9788968480911%26orderClick%3DJAj)
