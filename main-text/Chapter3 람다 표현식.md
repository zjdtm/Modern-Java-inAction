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

함수형 인터페이스는 오직 하나의 추상 메서드만 가지고 있다.

java.util.function 패키지에는 여러 가지 함수형 인터페이스를 제공하는데
책에서는 Predicate, Consumer, Function 인터페이스만 소개한다. 

나머지 함수형 인터페이스는 따로 공부를 해보도록 하겠다.

## Predicate 
java.util.function.Predicate<T> 인터페이스는 test라는 추상 메서드를 정의하며
test는 제네릭 형식 T의 객체를 인수로 받아 boolean을 반환한다.
```java
  @FunctionalInterface
  public interface Predicate<T> {
    boolean test(T t);
  }
```
## Consumer
java.util.function.Consumer<T> 인터페이스는 accept라는 추상 메서드를 정의하며
accept는 제네릭 형식 T의 객체를 인수로 받아 void를 반환한다.
```java
  @FunctionalInterface
  public interface Consumer<T>{
    void accept(T t);
  }
```

## Function
java.util.function.Function<T> 인터페이스는 apply라는 추상 메서드를 정의하며
apply는 제네릭 형식 T를 인수로 받아 제네릭 형식 R 객체를 반환한다.         
```java
  @FunctionalInterface
  public interface Function<T, R> {
    R apply(T t);
  }
```
자바에서는 기본형을 참조형으로 변환하는 기능인 **박싱**과 참조형을 기본형으로 변환하는 **언박싱** 기능이 있다.
이러한 방식을 자동으로 해주는 **오토박싱** 기능이 존재하는데 이러한 변환 과정은 비용이 많이 소모된다.
          
그래서 자바 8에서는 오토박싱 동작을 피할 수 있도록 특별한 버전의 함수형 인터페이스를 제공한다.

          DoublePredicate, IntConsumer, LongBinaryOperator, IntFunction, ToIntFunction<T>, IntToDoubleFunction          

함수형 인터페이스는 확인된 예외를 던지는 동작을 허용하지 않는다.
> 확인된 예외(checked exception)
- 컴파일시 발생하는 에러
- RuntimException 이외의 예외들을 뜻함          
그래서 예외를 던지는 람다 표현식을 만들려면 확인된 예외를 선언하는 함수형 인터페이스를 직접 정의하거나
람다를 try-catch 블럭으로 감싸야 한다.          

람다가 사용되는 콘텍스트를 이용해서 람다의 형식을 추론할 수 있다.
어떤 콘텍스트에서 기대되는 람다 표현식의 형식을 **대상 형식**이라고 부른다.          

```java
   List<Apple> heavierThan150g = filter(inventory, (Apple apple) -> apple.getWeight() > 150);       
```
1. filter 메서드의 선언을 확인한다.
2. filter 메서드는 두 번째 파라미터로 Predicate<Apple> 형식(대상 형식)을 기대한다.
3. Predicate<Apple>은 test라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스이다.
4. test 메서드는 Apple을 받아 boolean을 반환하는 함수 디스크립터를 묘사한다.
5. filter 메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 한다.
          
# 5. 메서드 참조 와 생성자 참조

메서드 참조를 이용하면 기존의 메서드 정의를 재활용해서 람다처럼 전달할 수 있다. 
즉 람다의 축양형이라고 생각할 수 있다.  

|람다|메서드 참조|          
|--------------------------------------------|-------------------------| 
| (Apple apple) -> apple.getWeight( ) | Apple::getWeight |
| ( ) -> Thread.currentThread( ).dumpStack( ) | Thread.currentThread( )::dumpStack |          
| (str, i) -> str.substring(i) | String::sbustring |
| (String s) -> System.out.println(s) | System.out::println |
| (String s) -> this.isValidName(s) | this::isValidName |    
          
메서드 참조를 만드는 방법
  
1. 정적 메서드 참조 
> 람다  : (args) -> ClassName.staticMethod(args)
    
> 메서드 참조   : ClassName::staticMethod
    
2. 다양한 방식의 인스턴스 메서드 참조
    
> 람다 : (arg0, rest) -> arg0.instanceMethod(rest)
    
> 메서드 참조 : ClassName::instanceMethod
  
3. 기존 객체의 인스턴스 메서드 참조 
>람다 : (args) -> expr.instanceMethod(args)
    
>메서드 참조 : expr::instanceMethod    

## 예시 
    람다          : ToIntFunction<String> stringToInt = (String s) -> Integer.parseInt(s);
    메서드 참조   : ToIntFunction<String> stringToInt = Integer::parseInt;
    
    람다          : BiPredicate<List<String>, String> contains = (list, element) -> list.contains(element);
    메서드 참조   : BiPredicate<List<String>, String> contains = List::contains;
    
    람다          : Predicate<String> startsWithNumber = (String string) -> this.startsWithNumber(string);
    메서드 참조   : Predicate<String> startsWithNumber = this::startsWithNumber;

생성자 참조는 ClassName::new 처럼 

클래스명과 new 키워드를 이용해서 기존 생성자의 참조를 만들 수도 있다.    

## 예시
    
    Supplier<Apple> c1 = Apple::new;
    Apple a1 = c1.get();
    
    BiFunction<Color, Integer, Apple> c3 = Apple::new;
    Apple a3 = c3.apply(GREEN, 110);
    
# 6. 람다 만들기

사과 리스트를 이용해서 정렬 기법을 동작 파라미터화, 익명 클래스, 람다 표현식, 메서드 참조 등을 사용해보자

## 1. 코드 전달
    
자바 8의 List API에서는 sort 메서드를 제공한다.
    
![image](https://user-images.githubusercontent.com/35757620/214518077-0d816562-192b-4bda-9547-dc239340391f.png)

sort 메서드는 Comparator 객체를 인수로 받는다. 이 때 다양한 전략을 전달할 수 있기 때문에

'sort의 동작은 파라미터화되었다'라고 말할 수 있다.
    
```java
   public class AppleComparator implements Comparator<Apple> {
    public int compare(Apple a1, Apple a2){
        return a1.getWeight().compareTo(a2.getWeight());
    }
   }
   inventory.sort(new AppleComparator());
```    

## 2. 익명 클래스 사용
    
한 번만 사용할 Comparator를 위 코드처럼 구현하는 것보다는 익명 클래스를 이용하는 것이 좋다.

```java
   inventory.sort(new Comparator<Apple>() {
    public int compare(Apple a1, Apple a2){
        return a1.getWeight().compareTo(a2.getWeight());
    }
   }
```    
    
## 3. 람다 표현식 사용

추상 메서드의 시그니처(함수 디스크립터)는 람다 표현식의 시그니처를 정의한다.
    
Comparator의 함수 디스크립터는 (T, T) -> int이므로 정확히 표현하면
  
(Apple, Apple) -> int로 표현할 수 있다.
    
```java
   inventory.sort((Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight()) 
```
자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 활용해서 람다의 파라미터 형식을 추론화할 수 있다.

```java
   inventory.sort((a1, a2) -> a1.getWeight().compareTo(a2.getWeight()));
``` 

Comparator는 Comparable 키를 추출해서 Comparator 객체로 만드는 Function 함수를 인수로 받는
정적 메서드 comparing을 포함한다.
    
![image](https://user-images.githubusercontent.com/35757620/214521270-8cb5e27a-08c2-4f09-b0d8-aff15b43ab25.png)    

comparing 메서드를 사용하면 다음과 같다.(더 공부해야 할 부분)

```java
    import static java.util.Comparator.comparing;
    inventory.sort(comparing(apple -> apple.getWeight()));
```
    
4. 메서드 참조 사용
    
마지작으로 메서드 참조를 사용하면 람다 표현식의 인수를 더 깔끔하게 전달할 수 있다.
```java
   inventory.sort(comparing(Apple::getWeight));
```

# 6. 마치며
- 람다 표현식은 익명 함수의 일종이다. 이름은 없지만, 파라미터 리스트, 바디, 반환 형식을 가지며 예외를 던질 수 있다.
- 람다 표현식으로 간결한 코드를 구현할 수 있다.
- 함수형 인터페이스는 하나의 추상 메서드만을 정의하는 인터페이스이다.
- 함수형 인터페이스를 기대하는 곳에서만 람다 표현식을 사용할 수 있다.
- 람다 표현식을 이용해서 함수형 인터페이스의 추상 메서드를 즉석으로 제공할 수 있으며 람다 표현식 전체가 함수형 인터페이스의 인스턴스로 취급된다.
- java.util.function 패키지는 Predicate<T>, Function<T,R>, Supplier<T>, Consumer<T>, BinaryOperator<T> 등을 포함해서 자주 사용하는 다양한 함수형 인터페이스를 제공한다.
- 자바 8은 Predicate<T>와 Funtion<T,R> 같은 제네릭 함수형 인터페이스와 관련한 박싱 동작을 피할 수 있는 IntPredicate, IntToLongFunction 등과 같은 기본형 특화 인터페이스도 제공한다.
- 실행 어라운트 패턴을 람다와 활용하면 유연성과 재사용성을 추가로 얻을 수 있다.
- 람다 표현식의 기대 형식을 대상 형식이라고 한다.
- 메서드 참조를 이용하면 기존의 메서드 구현을 재사용하고 직접 전달할 수 있다.
- Comparator, Predicate, Function 같은 함수형 인터페이스는 람다 표현식을 조합할 수 있는 다양한 디폴트 메서드를 제공한다.  
