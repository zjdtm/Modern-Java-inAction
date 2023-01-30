# 1. 필터링
![image](https://user-images.githubusercontent.com/35757620/215309780-27511d54-5965-4ab5-877e-3a579b708aea.png)

filter 메서드는 Predicate(프레디케이드, 불리언을 반환하는 함수)를 인수로 받아서 Predicate와 일치하는 모든 요소를 포함하는 스트림을 반환한다.

![image](https://user-images.githubusercontent.com/35757620/215309896-d73e494e-64d6-4b21-a020-15f371cf2243.png)

distinct 메서드는 고유 요소로 이루어지는 스트림을 반환한다. 즉, 중복을 제거해주는 기능을 한다.

# 2. 슬라이싱

![image](https://user-images.githubusercontent.com/35757620/215309954-14cbb22c-d05b-4ba2-b5dd-0cfabb0f08c8.png)

takeWhile 메서드는 Predicate를 인수로 받아서 Predicate를 모든 스트림에 적용해서 슬라이드할 수 있다.
> ## filter VS takeWhile
> 
> filter 메서드와 takeWhile 메서드는 비슷해보이지만 무한 스트림이거나 리스트가 이미 정렬되어 있는 상태에서는 차이점이 존재한다.
> takeWhile 메서드는 Predicate 조건에 맞지 않는 값을 찾을 때까지만 순회한다.
> ## 예제
> ```java
>List<Dish> specialMenu = Arrays.asList(
>        new Dish("season fruit", true, 120, Dish.Type.OTHER),
>        new Dish("prawns", false, 300, Dish.Type.FISH),
>        new Dish("rice", true, 350, Dish.Type.OTHER),
>        new Dish("chicken", false, 400, Dish.Type.MEAT),
>        new Dish("french fries", true, 530, Dish.Type.OTHER));
>  
>List<Dish> reverse_specialMenu = Arrays.asList(
>        new Dish("season fruit", true, 530, Dish.Type.OTHER),
>        new Dish("prawns", false, 400, Dish.Type.FISH),
>        new Dish("rice", true, 270, Dish.Type.OTHER),
>        new Dish("chicken", false, 300, Dish.Type.MEAT),
>        new Dish("french fries", true, 120, Dish.Type.OTHER));
> ```
> 320 칼로리 이하의 요리를 선택하는 코드를 작성한다면
> ```java
>    List<Dish> filteredMenu = specialMenu.stream()
>                                        .filter(dish -> dish.getCalories() < 320)
>                                        .collect(toList());
>    /* season fruit
>       prawns */
>    List<Dish> filteredMenu = reverse_specialMenu.stream()
>                                        .filter(dish -> dish.getCalories() < 320)
>                                        .collect(toList());   
>    /* season fruit
>       prawns */
>    
>    List<Dish> slicedMenu = specialMenu.stream()
>                                       .takeWhile(dish -> dish.getCalories() < 320)
>                                       .collect(toList());
>    /* season fruit
>       prawns */
>
>    List<Dish> slicedMenu = reverse_specialMenu.stream()
>                                       .takeWhile(dish -> dish.getCalories() < 320)
>                                       .collect(toList());
>    /* 결과 X */
>
>  ```

  
![image](https://user-images.githubusercontent.com/35757620/215311218-60a5b826-68c9-413b-80b9-115593c4eff1.png)

dropWhile 메서드는 Predicate 조건에 맞지 않는 지점까지 발견된 요소를 버린다.
```java
  List<Dish> slicedMenu2 = specialMenu.stream()
                                      .dropWhile(dish -> dish.getCalories() < 320)
                                      .collect(toList());
  /* rice
     chicken
     french fries */                                                                              
```
![image](https://user-images.githubusercontent.com/35757620/215311485-1c8e1f53-98a6-441f-86c1-95b2ae1ed9df.png)

limit 메서드는 스트림의 값의 크기를 제한하는 메서드이며 원하는 크기의 새로운 스트림을 반환해준다.
```java
  List<Dish> dishes = specialMenu.stream()
                                 .filter(dish -> dish.getCalories > 300)
                                 .limit(3)
                                 .collect(toList());
  /* 
    dishes = [rice, chicken, french fries]
  */
```                                                                                  
![image](https://user-images.githubusercontent.com/35757620/215311741-e821ba20-21b1-45f2-b7a6-c61c26df22aa.png)

skip 메서드는 처음 몇개의 요소를 건너뛰는는 메서드이다.
```java
  List<Dish> dishes = specialMenu.stream()
        .filter(dish -> dish.getCalories() > 300)
        .limit(3)
        .skip(2)
        .collect(toList());
  /*
    dishes = [french fries]
  */
```

# 3. 매핑
  특정 객체에서 특정 데이터를 선택하는 작업을 스트림 API에서는 map과 flatMap 메서드가 그 역할을 한다.
  
  ## map
  ![image](https://user-images.githubusercontent.com/35757620/215312790-ba9749cb-2744-4c51-a57c-95d471205a3f.png)
  
  Function 함수를 인수로 받아서 각 요소에 적용하여 새로운 요소로 매핑시켜주는 메서드이다.
  ### 예제
  ```java
    List<String> words = Arrays.asList("Modern", "Java", "In", "Action");
    List<Integer> wordLengths = words.stream()
                                     .map(String::length)
                                     .collect(toList());
    /*
      wordLengths = [6, 4, 2, 6]
    */
  ```
  
  ## flatMap
  ![image](https://user-images.githubusercontent.com/35757620/215312827-f8d8d873-11f6-4375-b7b2-ae8bf9ce7916.png)
  flatMap 메서드는 스트림을 하나의 평면화된 스트림으로 반환시켜주는 메서드이다.
  
  예제 코드를 보면 이해하기 더 수월해진다.
  ### 예제
> ["Java", "Python", "Javascript"] 리스트에서 중복을 제거한 고유문자로 이루어진 리스트를 반환하고자 한다.
  ex) ["J", "a", "v", "P", "y", "t", "h", "o", "n", "s", "c", "r", "i", "p"]
  그러면 다음과 같은 코드를 작성할 수 있다.
  
```java
       List<String[]> result = codingLanguage.stream()
            .map(word -> word.split(" "))
            .distinct()
            .collect(toList());
      /*
        map 메서드가 반환한 스트림의 형식은 Stream<String[]>이다.
        [["J", "a", "v"], ["P", "y", "t", "h", "o", "n"], ["s", "c", "r", "i", "p"]]
      */
```
원하던 결과가 나오지 않았다. 원하는 방식은 Stream<String>을 반환하는 것인데 해결책은 flatMap 메서드이다.
flatMap 메서드를 사용하면 스트림의 각 값을 다른 스트림으로 만든 다음에 모든 스트림을 하나의 스트림으로 연결하는 기능을 수행한다.
```java
      List<String> result_flatMap = codingLanguage.stream()
                   .map(word -> word.split(" "))
                   .flatMap(Arrays::stream)  // Arrays::stream : 배열을 스트림으로 만들어줌
                   .distinct
                   .collect(toList());
      System.out.println("result_flatMap = " + result_flatMap);
      // result_flatMap = [J, a, v, P, y, t, h, o, n, s, c, r, i, p]
```
  
# 4. 검색과 매칭
  
  스트림 API 는 allMatch, anyMatch, noneMatch, findFirst, findAny 등 다양한 유틸리티 메서드를 제공한다.
  
  ## allMatch
  ![image](https://user-images.githubusercontent.com/35757620/215372400-6128ec1d-cc70-46a6-b59e-837eb1587ba0.png)
  Predicate가 모든 요소와 일치하는지 검사하는 메서드이다.
  
  ## anyMatch
  ![image](https://user-images.githubusercontent.com/35757620/215372378-28206ac8-24ff-4da8-8cd9-d3922213bfd4.png)
  Predicate가 적어도 한 요소와 일치하는지 확인
  
  ### 예제
  ```java
    List<String> codingLanguage = Arrays.asList("java", "python", "c", "c#", "c++", "go", "javascript");

        boolean isJava = codingLanguage
                .stream().anyMatch(l -> l.equals("java"));
        System.out.println("isJava = " + isJava);
  ```
  ## noneMatch
  ![image](https://user-images.githubusercontent.com/35757620/215372410-5a948e5f-bfa6-4708-8215-200b964ddce6.png)
  Predicate와 일치하는 요소가 없는지 확인하는 메서드이다. allMatch와는 반대 연산을 수행한다.
  
  이렇게 anyMatch, allMatch, noneMatch 메서드를 **쇼트서킷** 기법이라고 한다.
  >쇼트 서킷
  자바의 &&, ||와 같은 연산을 활용하는 메서드로 전체 스트림을 처리하지 않고도 결과를 반환할 수 있다.
  원하는 요소를 찾으면 그 즉시 결과를 반환하는 것이 가능하다.
  
  ## findFirst
  ![image](https://user-images.githubusercontent.com/35757620/215372432-24d90786-5973-4a00-b139-7d974be2da5d.png)
  스트림에서 첫 번째 요소를 찾는 메서드이다.
  
  ## findAny
  ![image](https://user-images.githubusercontent.com/35757620/215372442-0f6dcc90-1aeb-447d-bd52-f8779c359025.png)
  현재 스트림에서 임의의 요소를 반환시켜주는 메서드이다.
  
  >Optional 
  Optional<T> 클래스는 값의 존재나 부재 여부를 표현하는 컨테이너 클래스이다.
  null을 쉽게 처리하기 위해서 만들어졌다.
  
# 5. 리듀싱
  
  스트림 요소를 조합해서 더 복잡한 질의를 표현하는 방법
  
  반복적으로 처리해야 하는 질의를 **리듀싱 연산**(모든 스트림 요소를 처리해서 값으로 도출하는)이라고 한다.
  
  ![image](https://user-images.githubusercontent.com/35757620/215375469-5691c76b-b1b7-42e5-9264-6c3a6bb323d5.png)
  첫 번째 인수에는 초깃값 
  
  두 번째 인수에는 두 요소를 조합해서 새로운 값을 만드는 BinaryOperator<T> 
  
  reduce는 이렇게 두 개의 인수를 가지고 있다.
  
  ## 예제
  ```java
     List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
     int result = numbers.stream().reduce(0, (a, b) -> a+b);
     System.out.println("result = " + result);
     // result = 15
  ```
  
  최대값과 최솟값을 구할때도 rduce를 사용할 수 있다.
  
  ## 예제
  ```java
    List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
  
    Optional<Integer> max = numbers.stream().reduce(Integer::max);
    Optional<Integer> min = numbers.stream().reduce(Integer::min);

    System.out.println("max = " + max); // Optional[1]
    System.out.println("min = " + min); // Optional[5]
  ```
  > Optional<Integer>를 반환하는 이유는 스트림에 아무 요소가 없으면 reduce는 합계를 반환할 수 없기 때문에
  Optional 객체로 감싸서 결과를 반환하는 것이다.  
  
# 6. 숫자형 스트림
  
  자바 8에서는 스트림 API 숫자 스트림을 효율적으로 처리할 수 있도록 **기본형 특화 스트림**을 제공한다.
  
  기본형 특화 스트림에는 IntStream, DoubleStream, LongStream 세 가지 인터페이스가 있는데 sum, max, 숫자 관련 리듀싱 연산 수행 메서드 및 객체 스트림을 복원 기능을 가지고 있다.
  
  이런 특화 스트림의 장점은 박싱 과정에서 일어나는 효율성을 증가시켜주는 것이다.
  
  ![image](https://user-images.githubusercontent.com/35757620/215390039-55e7a4f6-84a4-4cf4-8f19-9f9c73e71db2.png)
  > mapToInt, mapToDouble, mapToLong 세 가지 메서드는 각 각 특화된 스트림으로 변환시켜준다.
  >
  > boxed 메서드를 사용하면 특화 스트림을 일반 스트림으로 변환시켜줄 수도 있다.
  > ```java
  >    IntStream intStream = menu.stream().mapToInt(Dish::getCalories); // 스트림을 숫자 스트림으로 변환
  >    Stream<Integer> stream = intStream.boxed();                      // 숫자 스트림을 스트림으로 변환
  > ```
  
  또한 숫자 범위를 지정할 수 있는 range 메서드와 rangeClosed 메서드가 존재하는데
  
  IntStream, DoubleStream, LongStream 세 가지 인터페이스에 정적 메서드로 제공한다.
  
  range 메서드는 시작값과 종료값이 결과에 포함되지 않지만 rangeClosed 메서드는 시작값과 종료값이 결과에 포함된다.
  >IntStream.range(1, 100)은 1과 100을 포함하지 않지만 IntStream.rangeClosed(1, 100)은 1과 100을 포함한다.
  
# 7. 스트림 만들기
  
  스트림은 일련의 값, 배열, 파일, 무한 스트림 등 다양한 방식으로 스트림을 만들수가 있다.
  
  ![image](https://user-images.githubusercontent.com/35757620/215395669-4df5287f-b9df-4446-b3c0-388989f085b0.png)
  Stream.of 메서드를 사용하면 임의의 수를 스트림으로 만들 수 있다.
  ```java
    Stream<String> stream = Stream.of("Modern", "Java", "In", "Action");
    stream.map(String::toUpperCase).forEach(System.out::println);
    /*
      MODERN
      JAVA
      IN
      ACTION
    */
  ```
 
  ![image](https://user-images.githubusercontent.com/35757620/215396054-9d0a19e3-a83c-45fb-87d6-0918477b4902.png)
  
  Stream.ofNullable 메서드를 사용하면 null이 될 수 있는 객체로 스트림을 만들 수 있다.

  ![image](https://user-images.githubusercontent.com/35757620/215396385-0235ea0f-fc2b-47ef-9368-fd8a05d828fb.png)
  
  Arrays.stream 메서드를 사용하면 배열로 스트림을 만들 수 있다.
  ```java
       int[] numbers2 = {2, 4, 6, 9, 20};
       int sum = Arrays.stream(numbers2).sum(); 
       System.out.println("sum = " + sum); // sum = 41
  ```
  
  ![image](https://user-images.githubusercontent.com/35757620/215396857-8480f446-e0d5-47b9-b570-dfe0d7ce86f7.png)
  
  Files.lines로 파일의 각 행 요소를 반환하는 스트림을 얻을 수 있다.
  
  스트림 API는 무한 스트림을 만들 수 있는 Stream.iterate와 Stream.generate를 제공한다.
  
  iterate는 기존 결과에 의존해서 순차적으로 연산을 수행한다.
  ## 예제
  ### iterate 메서드를 이용한 피보나치수열의 집합 만들기
  ```java
    Stream.iterate(new int[]{0, 1}, i -> new int[]{i[1], i[0] + i[1]})
        .limit(20)
        .forEach(t -> System.out.println("(" + t[0] + "," + t[1] + ")"));
  ```
  generate는 iterate와 달리 생성된 각 값을 연속적으로 계산하지 않는다.
  
# 8. 마치며
- 스트림 API를 이용하면 복잡한 데이터 처리 질의를 표현할 수 있다. 
- filter, distinct, takeWhile, dropWhile, skip, limit 메서드로 스트림을 필터링하거나 자를 수 있다.
- 소스가 정렬되어 있다는 사실을 알고 있을 때 takeWhile과 dropWhile 메서드를 효과적으로 사용할 수 있다.
- map, flatMap 메서드로 스트림의 요소를 추출하거나 변환할 수 있다.
- findFirst, findAny 메서드 스트림의 요소를 검색할 수 있다. allMatch, noneMatch, anyMatch 메서드를 이용해서 주어진 프레디케이트와 일치하는
  요소를 스트림에서 검색할 수 있다.
- 이들 메서드는 쇼트서킷, 즉 결과를 찾는 즉시 반환하며, 전체 스트림을 처리하지는 않는다.
- reduce 메서드로 스트림의 모든 요소를 반복 조합하며 값을 도출할 수 있다. 예를 들어 reduce로 스트림의 최댓값이나 모든 요소의 합계를 계산할 수 있다.
- filter, map 등은 상태를 저장하지 않는 상태 없는 연산이다. reduce 같은 연산은 값을 계산하는데 필요한 상태를 저장한다. sorted, distinct 등의 메서드는 새로운 스트림을 반환하기에 앞서 스트림의 모든 요소를 버퍼에 저장해야 한다. 이런 메서드들은 상태 있는 연산이라고 부른다.
- IntStream, DoubleStream, LongStream은 기본형 특화 스트림이다. 이들 연산은 각각의 기본형에 맞게 특화되어 있다.
- 컬렉션뿐 아니라 값, 배열, 파일, iterate와 generate 같은 메서드로 스트림을 만들 수 있다.
- 무한한 개수의 요소를 가진 스트림을 무한 스트림이라 한다.
