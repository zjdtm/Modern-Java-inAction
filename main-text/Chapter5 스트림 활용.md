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
# 5. 리듀싱
# 6. 실전 연습
# 7. 숫자형 스트림
# 8. 스트림 만들기
# 9. 마치며
