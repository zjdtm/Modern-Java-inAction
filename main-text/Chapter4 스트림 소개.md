# 1. 스트림이란 무엇인가?
  스트림은 자바 8 API에 새로 추가된 기능이며 컬렉션 반복의 성능을 높이기 위해 만들어 졌다.
  
  스트림은 '데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소' 라는 뜻이다.

  >스트림의 특징 및 장점
  >- **선연형** : 루프문이나 if 조건문 등의 제어 블록을 사용할 필요 없이 선언형으로 코드를 작성할 수 있어서 더 간결하고 가독성이 좋아진다.
  >- **조립할 수 있음** : filter, sorted, map, collect 같은 여러 빌딩 블록 연산을 연결해서 복잡한 데이터 처리 파이프라인을 만들 수 있어서 유연성이 좋아진다.
  >- **병렬화** : 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬처리가 가능해서 성능이 좋아진다.
  
  ## 예시
  ```java
    import static java.util.stream.Collectors.toList;
    List<String> threeHighCaloricDishNames 
        = menu.stream()
              .filter(dish -> dish.getCalories() > 300)
              .map(Dish::getName)
              .limit(3)
              .collect(toList());
    System.out.println(threeHighCaloricDishNames);         
  ```
  > '데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소'
  >  - 소스 : 요리 리스트(menu)
  >  - 연속된 요소 : 데이터 소스는 연속된 요소를 스트림에 제공
  >  - 데이터 처리 연산 : filter, map, limit, collect 

# 2. 컬렉션(내부 반복) 과 스트림(외부 반복)

  컬렉션과 스트림의 가장 큰 차이점은 데이터를 언제 계산하느냐이다.
  
  컬렉션은 현재 자료구조가 포함하는 모든 값을 메모리에 저장하는 자료구조이다.즉, 컬렉션의 모든 요소는 컬렉션에 추가하기 전에 미리 계산되어야 한다.
  
  반면 스트림은 이론적으로 요청할 때만 요소를 계산하는 고정된 자료구조이다. 그리고 스트림은 단 한 번만 소비할 수 있어서 다시 사용하려면 새로운 스트림을 만들어야 한다. 또 다른 차이점은 컬렉션은 외부반복이고 스트림은 내부반복이다.
  
  외부반복은 사용자가 직접 요소를 반복해야 하지만 내부반복은 반복을 알아서 처리하고 결과 스트림값을 어딘가에 저장해준다.
  
  ```java
    // 외부 반복
    List<String> names = new ArrayList<>();
    for(Dish dish : menu){
      names.add(dish.getName());
    }
    
    // 내부 반복
    List<String> names = menu.stream()
                             .map(Dish::getName)
                             .collect(toList());
  ```


# 3. 중간 연산과 최종 연산

  스트림 연산은 크게 중간 연산과 최종 연산 두 그룹으로 나눌 수 있다.
  ```java
    List<String> names = menu.stream()
                             .filter(dish -> dish.getCalories() > 300) // 중간 연산
                             .map(Dish::getName)                       // 중간 연산
                             .limit(3)                                 // 중간 연산
                             .collect(toList());                       // 최종 연산
  ```
  
  ## 중간 연산
    filter나 sorted 같은 중간 연산은 다른 스트림을 반환한다. 따라서 여러 중간 연산을 연결해서 질의를 만들 수 있다.
    또한 단말 연산을 스트림 파이프라인에 실행하기 전까지는 아무 연산도 수행하지 않는다.
    대표적인 중간 연산은 filter, map, limit, sorted, distinct 등이 있다.
  
  ## 최종 연산
    스트림 파이프라인에서 결과를 도출한다. 보통 최종 연산에 의해 List, Integer, void 등 스트림 이외의 결과가 반환된다.
    대표적인 최종 연산은 forEach, count, collect 등이 있다.
    
# 마치며
  - 스트림은 소스에서 추출된 연속 요소로, 데이터 처리 연산을 지원한다.
  - 스트림은 내부 반복을 지원한다. 내부 반복은 filter, map, sorted 등의 연산으로 반복을 추상화한다.
  - 스트림에는 중간 연산과 최종 연산이 있다.
  - 중간 연산은 filter와 map처럼 스트림을 반환하면서 다른 연산과 연결되는 연산이다. 중간 연산을 이용해서 파이프라인을 구성할 수 있지만 중간 연산으로는 어떤 결과도 생성할 수 없다.
  - forEach나 count처럼 스트림 파이프라인을 처리해서 스트림이 아닌 결과를 반환하는 연산을 최종 연산이라고 한다.
  - 스트림의 요소는 요청할 때 게으르게 계산된다.
    
