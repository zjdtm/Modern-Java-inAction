# Collectors 클래스로 컬렉션을 만들고 사용하기

![image](https://user-images.githubusercontent.com/35757620/215927946-d3646da6-e20a-49a9-af9e-c3b42e4de97f.png)

Stream API 에 collect 메서드를 확인해보면 Collector 인터페이스의 구현을 인자로 받는다.

Collector 인터페이스 구현은 스트림의 요소를 어떤 식으로 도출할지 지정한다.

대표적으로 toList, groupingBy가 있는데 toList는 '각 요소를 리스트로 만들어라' groupingBy는 '각 요소 리스트를 값으로 포함하는 Map으로 만들어라' 이다.

즉, 스트림에 collect를 호출하면 스트림의 요소에(컬렉터로 파라미터화된) 리듀싱 연산이 수행된다.

**Collectors** 유틸리티 클래스는 이런 컬렉터 인스턴스를 손쉽게 생성할 수 있는 정적 팩토리 메서드를 제공한다.

**Collectors** 메서드의 기능은 **스트림 요소를 하나의 값으로 리듀스하고 요약, 요소 그룹화, 요소 분할** 세 가지로 나눌 수 있다.

# 하나의 값으로 데이터 스트림 리듀스하기

  Stream.collect 메서드의 인수인 컬렉터로 스트림의 항목을 컬렉션으로 재구성 할 수 있다. 
  
  즉, 컬렉터로 스트림의 모든 항목을 하나의 결과로 합칠 수 있다.
  
  **Collectors** 메서드 중에 리듀싱과 요약하는 메서드를 살펴보면
- ** counting()** : 요소의 수를 계산하는데 사용
  ```java
    List<String> pl = Arrays.asList("java", "go", "javascript", "python", "c", "c#", "c++");
    Long count = pl.stream().collect(counting());

    System.out.println("count = " + count);
    // count = 7 ("java", "go", "javascript", "python", "c", "c#", "c++") 총 7개의 요소 수를 출력
  ```
- ** maxBy()** : 스트림의 최댓값을 구하는데 사용 Comparator를 인수로 받는다.
- ** minBy()** : 스트림의 최솟값을 구하는데 사용 Comparator를 인수로 받는다.
  ```java    
    Optional<String> max = pl.stream().collect(maxBy(comparingInt(String::length)));
    System.out.println("collect = " + collect);

    Optional<String> min = pl.stream().collect(minBy(comparingInt(String::length)));
    System.out.println("collect = " + collect);

    max = Optional[javascript] // 문자열의 길이가 가장 긴 javascript
    min = Optional[c]          // 문자열의 길이가 가잔 작은 c 
  ```
- **summingInt(), summingLong(), summingDouble()** : 총 합계을 요약하는 메서드
  ```java
    Integer sumLength = pl.stream().collect(summingInt(String::length));
    System.out.println("sumLength = " + sumLength); // sumLength = 28
  ```
- ** averagingInt(), averagingLong(), averagingDouble()** : 총 평균을 요약하는 메서드
  ```java
    Double avgLength = pl.stream().collect(averagingInt(String::length));
    System.out.println("avgLength = " + avgLength); // avgLength = 4.0
  ```
- ** summarizingInt(), summarizingLong(), summarizingDouble() **: 요소 수, 합계, 평균, 최댓값, 최솟값 등을 하나로 요약해준다.
  또한 아래 코드를 실행하면 IntSummaryStatistics 클래스로 모든 정보가 수집되는데 IntSummaryStatistics 클래스 타입을 반환한다.
  ```java
     IntSummaryStatistics result = pl.stream().collect(summarizingInt(String::length));
     System.out.println("result = " + result); // result = IntSummaryStatistics{count=7, sum=28, min=1, average=4.000000, max=10}
  ```
- **joining()** : 스트림의 각 객체에 toString 메서드를 호출해서 하나의 문자열로 연결시켜주는 메서드이다.
  ```java
    String allStrings = pl.stream().collect(Collectors.joining(", "));
    System.out.println("allStrings = " + allStrings);
    // allStrings = java, go, javascript, python, c, c#, c++
    // joining 메서드에 구분 문자열을 넣어서 문자열을 구분할 수 있다.
  ```

# 특별한 리듀싱 요약 연산
reducing() : 범용 리듀싱 요약 연산으로 다양한 방식으로 연산을 수행 할 수 있다.
![image](https://user-images.githubusercontent.com/35757620/215959142-a12b63fd-3521-44f6-b9f5-d6ca81a2cf3d.png)
![image](https://user-images.githubusercontent.com/35757620/215959195-6e80a732-c5e6-438b-8ae0-4e6a3279581c.png)
![image](https://user-images.githubusercontent.com/35757620/215958978-69588375-6f8d-4d27-90dc-14eda5961edc.png)

다음과 같이 reducing() 메서드는 한 개의 인수 또는 여러 개의 인수를 받는다.

```java
   Integer totalCount = pl.stream().collect(reducing(0, String::length, Integer::sum));
   Optional<String> longLength = pl.stream().collect(reducing((a, b) -> a.length() > b.length() ? a : b));

   System.out.println("totalCount = " + totalCount);
   System.out.println("longString = " + longLength);
   //totalCount = 28
   //longString = Optional[javascript]
```
    Integer totalCount = pl.stream().collect(reducing(0, String::length, Integer::sum));
                                                      |         |             |
                                                      |         |             |
                                                    초깃값   변환 함수      합계 함수
                                                    
# 데이터 그룹화 분할

Collectors.groupingBy 메서드를 사용하면 한 줄의 코드로 그룹화를 구현할 수 있다.

groupingBy 메서드 인자에 함수를 넣어주면 함수를 기준으로 스트림이 그룹화한다. 이때 함수를 분류 함수라고 부른다.



# 자신만의 커스텀 컬렉터 개발
