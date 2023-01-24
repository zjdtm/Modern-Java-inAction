# 1. 변화하는 요구사항에 대응

우리는 어떤 상황에서 일을 하든 소비자 요구사항은 항상 바뀐다. 변화하는 요구사항은 소프트웨어 엔지니어링에서 피할 수 없는 문제다.

동작 파라미터화를 이용하면 자주 바뀌는 요구사항에 효과적으로 대응할 수 있다.

농장 재고목록 애플리케이션에 리스트에서 녹색 사과만 필터링하는기능을 추가한다고 예시를 들어보면
다음과 같은 코드를 작성할 수 있다.
```java
  enum Color { RED, GREEN }
  
  public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory){
      if(GREEN.equals(apple.getColor()){
        result.add(apple);
      }
    }
    return result;
  }
```

그러다가 빨간 사과도 필터링 해야 하고 파란, 노란, 주황 색 등 다양한 색이
필터링 하고 싶어진다면 filterGreenApples 메서드를 복사 붙여넣기 해서
메서드 이름과 원하는 색에 이름으로 변경하면 가능하지만 매번 이렇게 하는 것으로는 변화에 대응할 수가 없다.
> 거의 비슷한 코드가 반복 존재한다면 그 코드를 추상화한다.

메서드에 **색**을 파라미터화에서 추가하면 좀 더 유연한 코드가 될 것 같다.
```java
  public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color){
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory){
      if(apple.getColor().equals(color)){
        result.add(apple);
      }
    }
    return result;
  }
  
  List<Apple> greenApples = filterApplesByColor(inventory, GREEN);
  List<Apple> redApples = filterApplesByColor(inventory, RED);
```

그러다가 이제는 색이 아닌 **무게**로 필터링을 추가해야하는 상황이오면 
색과 마찬가지로 무게도 파라미터화에서 추가하면 다음과 같은 코드가 될 것이다.

```java
  public static List<Apple> filterApplesByWeight(List<Apple> inventory, int weight){
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory){
      if(apple.getWeight() > weight){
        result.add(apple);
      }
    }
    return result;
  }
```

그렇다면 색과 무게를 합친 filter라는 메서드도 만들어도 그럴싸한 코드가 될 것 같다.
```java
  public static List<Apple> filterApples(List<Apple> inventory, Color color, int weight, boolean flag){
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory){
      if((flag && apple.getColor().equals(color)) || (!flag && apple.getWeight() > weight)) {
        result.add(apple);
    }
  }
  return result;
 }
 
 List<Apple> greenApples = filterApples(inventory, GREEN, 0, true);
 List<Apple> heavyApples = filterApples(inventory, null, 150, false);
```
작성해보니 원하던 코드가 아니게 되어버렸다. 만약에 녹색 사과 중에 무거운 사과를 필터링 해야 하는 상황이
오면 난감하다. 이처럼 어떤 기준으로 사과를 필터링할 것인지 효과적으로 전달할 수 있게 
**동작 파리미터화**를 이용해야 한다는 것을 깨닫게 되었다.

# 2. 동작 파라미터화

어떤 기준으로 사과를 필터링할 것인지 선택 조건을 결정하는 인터페이스를 정의 하면 다음과 같다.
```java
  public interface ApplePredicate {
    boolean test (Apple apple);
  }
  
  // 무거운 사과만 선택
  public class AppleHeavyWeightPredicate implements ApplePredicate {
    public boolean test(Apple apple){
      return apple.getWeight() > 150;
    }
  }
  
  // 녹색 사과만 선택
  public class AppleGreenColorPredicate implements ApplePredicate {
    public boolean test(Apple apple){
      return GREEN.equals(apple.getColor());
    }
  }
```

이처럼 filter 메서드가 다르게 동작하는 패턴을 **전략 디자인 패턴** 이라고 한다.

이제 filterApples 메서드가 ApplePredicate 객체를 인수로 받도록 **추상적 조건**으로 리팩터링 하면 다음과 같다.
```java
  public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory){
      if(p.test(apple)){
        result.add(apple);
      }
    }
    return result;
  }
```

이제 150그램 이상의 빨간 사과를 필터링 해야 한다면 ApplePredicate를 구현하는 클래스를
만들기만 하면 된다.
```java
  public class AppleRedAndHeavyPredicate implements ApplePredicate {
    public boolean test(Apple apple){
      return RED.equals(apple.getColor())
        && apple.getWeight() > 150;
    }
  }
  
  List<Apple> redAndHeavyApples = filterApples(inventory, new AppleRedAndHeavyPredicate());
```

하지만 여러 클래스를 구현해서 인스턴스화 하는 과정을 좀 더 간소화 시키는 방법이 없을까?
이러한 문제의 해결책은 **익명 클래스**이다.

# 3. 익명 클래스

익명 클래스는 클래스의 선언과 인스턴스화를 동시에 수행할 수 있기 때문에 코드의 양을 줄일 수 있다.

```java
  List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple){
      return RED.equals(apple.getColor());
    }
  });
```

하지만 여전히 익명 클래스를 사용해도 반복되는 코드 즉 새로운 동작을 정의하는 메서드를
구현해야 하는 점은 변하지 않는다.

# 4. 람다 표현식

이를 자바 8의 람다 표현식을 이용하면 코드를 더욱 간결하게 정리할 수가 있다.

```java
  List<Apple> result = filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));
``` 

# 마치며
- 동작 파라미터화에서는 메서드 내부적으로 다양항 동작을 수행할 수 있도록 코드를 메서드 인수로 전달한다.
- 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있으며 나중에 엔지니어링 비용을 줄일 수 있다.
- 코드 전달 기법을 이용하면 동작을 메서드의 인수로 전달할 수 있다. 하지만 자바 8 이전에는 코드를 지저분하게 구현해야 했다. 익명 클래스로도
  어느 정도 코드를 깔끔하게 만들 수 있지만 자바 8에서는 인터페이스를 상속받아 여러 클래스를 구현해야 하는 수고를 없앨 수 있는 방법을 제공한 다.
- 자바 API의 많은 메서드는 정렬, 스레드, GUI 처리 등을 포함한 다양한 동작으로 파라미터화할 수 있다.
