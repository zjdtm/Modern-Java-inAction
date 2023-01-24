# 1. 람다란 무엇인가?

람다라는 용어는 람다 미적분학 학계에서 개발한 시스템에서 유래했다. 

> 람다의 특징
- 익명 : 메서드와 달리 이름이 없어서 익명으로 표현한다.
- 함수 : 메서드처럼 특정 클래스에 종속되지 않아서 함수라고 부른다. 하지만
          파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함한다.
- 전달 : 람다 표현식을 메서드 인수로 전달하거나 저장할 수 있다.
- 간결성 : 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없다.          

람다 표현식은 메서드로 전달할 수 있는 익명 함수를 단순화한 것이라고 할 수 있으며 파라미터, 화살표, 바디로 이루어진다.

또한 return이 함축되어 있어서 return 문을 명식적으로 사용하지 않아도 된다.

```java
 (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
 |                  |  | |                                       |
 |__________________|  | |_______________________________________|
     람다 파라미터      |               람다 바디
                     화살표

```
# 2. 어디에, 어떻게 람다를 사용하는가?

람다 표현식은 **함수형 인터페이스**라는 문맥에서 사용할 수 있다.

함수형 인터페이스란 정확히 하나의 추상 메서드로 지정하는 인터페이스이며
Comparator, Runnable, ActionListener, Callable, PrivilegedAction 등 존재한다.

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으므로
전체 표현식을 함수형 인터페이스의 인스턴스로 취급할 수 있다.

함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가리킨다.
> 메서드 시그니처 : 메서드의 정의에서 메서드 이름과 매개변수 리스트의 조합을 가리킨다.

람다 표현식의 시그니처를 서술하는 메서드를 **함수 디스크립터**라고 부른다.

```java
  execute(() -> {});
  public void execute(Runnable r) {
    r.run();
  }
  // 람다 표현식 () -> {}의 시그니처는 () -> void
  // Runnable의 추상 메서드 run의 시그니처와 일치
  
  public Callable<String> fetch() {
    return () -> "Tricky example ;-)";
  }
  // Callable<String> 메서드의 시그니처는 () -> String이다.
  // () -> "Tricky example ;-)" 는 () -> String 시그니처와 일치
  
  Predicate<Apple> p = (Apple a) -> a.getWeight();
  // 람다 표현식 (Apple a) -> a.getWeight()의 시그니처는 (Apple) -> Integer이다.
  // Predicate<Apple> : (Apple) -> boolean의 test 메서드의 시그니처와 일치하지 않음
```
# 3. 실행 어라운드 패턴

람다와 동작 파라미터화로 유연하고 간결한 코드를 구현하는 데 도움을 주는 예제를 살펴보면

```java
  public String processFile() throws IOException {
    try(BufferedReader br = new BufferedReader(new FileReader("data.txt"))){
      return br.readLine();
    }
  }
```
위에 코드처럼 try-with-resources 구문을 사용해서 자원을 열고, 처리한 후, 명시적으로 닫을 필요 없이간결하게 코드를 작성하였다.

실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 갖는데 이를 **실행 어라운트 패턴**이라고 부른다.

그렇다면 두 줄을 읽거나 자주 사용되는 단어를 반환하려면 어떻게 해야하는가 
기존의 설정, 정리 과정은 재사용하고 processFile의 동작을 파라미터화 하면 가능하다.

```java
  String result = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

함수형 인터페이스 자리에 람다를 사용할 수 있다. 따라서 BufferedReader -> String과 IOException을 던질 수 있는 시그니처와
일치하는 함수형 인터페이스를 만들어야 한다.
```java
@FunctionalInterface
  public interface BufferedReaderProcessor {
    String process(BufferedReader b) throws IOException;
  }
```
정의한 인터페이스를 processFile 메서드의 인수로 전달할 수 있다.
```java
  public String processFile(BufferedReaderProcessor p) throws IOException {
      ...
  }
```

람다 표현식으로 함수형 인터페이스의 추상 메서드 구현을 직접 전달할 수 있으며 전달된 코드는 함수형 인터페이스의
인스턴스로 전달된 코드와 같은 방식으로 처리한다.

```java
  public String processFile(BufferedReaderProcessor p) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader("data.txt"))) {
      return p.process(br);
    }
   }
```

이제 람다를 이용해서 다양한 동작을 processFile 메서드로 전달할 수 있다.
```java
  String oneLine = processFile((BufferedReader br) -> br.readLine());
  
  String twoLines = processFile((BufferedReader br) -> br.readLine() + br.readLine());
```

# 4. 함수형 인터페이스, 형식 추론
# 5. 메서드 참조
# 6. 람다 만들기
