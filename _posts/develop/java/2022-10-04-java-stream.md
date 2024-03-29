---
title: "[Java] Stream (스트림)"
category: Java
---

# Stream (스트림)

> 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소
> 

컬렉션 반복을 멋지게(?) 처리하는 기능

멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리 가능

특징

- 가독성 향상
- 유연성 향상
- 성능 향상

컬렉션과 마찬가지로 연속된 값 집합의 인터페이스를 제공함

- 컬렉션: ArrayList or LinkedList 등 어떤 것을 사용할 것인지에 대한 시공간 복잡성과 관련된 저장 및 접근 연산이 주, **데이터 중심**
- 스트림 : filter, sorted, map 처럼 표현 계산식이 주, **계산 중심**

제공된 소스가 정렬되어 있다면, 스트림을 생성해도 정렬이 유지되어 있음

filter, map, reduce, find, match, sort 등 데이터 조작 가능

Pipelining

> 스트림 연산끼리 연결해서 커다란 파이프라인을 구성할 수 있도록 스트림 자신을 반환함
Laziness, Short-circuiting과 같은 최적화 가능
> 

Internal Repitition

> 내부 반복 지원
> 

## Global

```java
@Getter
@ToString
@AllArgsConstructor
public class Dish {

    private final String name;
    private final boolean vegetarian;
    private final Integer calories;
    private final DishType dishType;
}
```

```java
public enum DishType {
    MEAT, FISH, OTHER
}
```

## As-is Vs. To-be

```java
@Slf4j
@Component
public class CompareAsIs {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Compare AS Is =====");
        asIs();
        toBe();
        getThreeHighCaloricDishNames();
    }

    public void getThreeHighCaloricDishNames() {
        List<Dish> menu = Dishes();

        List<String> threeHighCaloricDishNames = menu.stream()
            .filter(d -> d.getCalories() > 300) // 람다를 인수로 받아 스트림에서 특정 요소 제외
            .map(Dish::getName) // 람다를 이용해서 요소를 변환하거나 정보를 추출
            .limit(3) //스트림의 크기 축소(truncate)
            .collect(Collectors.toList()); // 파이프라인 처리 후 스트림을 다른 형식으로 결과 변환

        log.debug("threeHighCaloricDishNames : {}", threeHighCaloricDishNames);
    }

    public void toBe() {
        List<Dish> menu = Dishes();

        List<String> lowCaloricDishesName = menu.stream().filter(d -> d.getCalories() < 400) // 조건
            .sorted(Comparator.comparing(Dish::getCalories)) // 정렬
            .map(Dish::getName) // 추출
            .collect(Collectors.toList()); // 저장

        lowCaloricDishesName.stream().forEach(d -> log.debug("toBe.dishName: {}", d)); // 로깅
    }

    public void asIs() {
        List<Dish> menu = Dishes();

        // Garbage Variable
        List<Dish> lowCaloricDishes = new ArrayList<>();

        for (Dish dish : menu) {
            if (dish.getCalories() < 400) {
                lowCaloricDishes.add(dish);
            }
        }

        Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
            public int compare(Dish dish1, Dish dish2) {
                return Integer.compare(dish1.getCalories(), dish2.getCalories());
            }
        });

        List<String> lowCaloricDishName = new ArrayList<>();

        for (Dish dish : lowCaloricDishes) {
            lowCaloricDishName.add(dish.getName());
        }

        for (String dishName : lowCaloricDishName) {
            log.debug("asIs.getName() = {}", dishName);
        }

    }

    private static List<Dish> Dishes() {
        return Arrays.asList(
            new Dish("pork", false, 800, DishType.MEAT),
            new Dish("beef", false, 700, DishType.MEAT),
            new Dish("chicken", false, 400, DishType.MEAT),
            new Dish("french", true, 530, DishType.OTHER),
            new Dish("rice", true, 350, DishType.OTHER),
            new Dish("season fruit", true, 120, DishType.OTHER),
            new Dish("pizza", true, 550, DishType.OTHER),
            new Dish("prawns", false, 330, DishType.MEAT),
            new Dish("salmon", false, 450, DishType.MEAT)

        );
    }

}
```

## Stream and Collection (스트림과 컬렉션)

데이터를 언제 계싼하느냐가 가장 큰 차이

| Stream | Collection |
| --- | --- |
| 요청 시에만 요소 계산 | 모든 값을 메모리에 저장 |
| 요소 추가 및 삭제 불가 | 요소 추가 및 삭제 가능 |
| 계산 중간에 결과를 확인할 수 있음 | 계산이 완료될 때까지 기다려야 함 |
| 반복을 알아서 처리하고 스트림 값을 어딘가에 저장함(내부 반복) | 사용자가 직접 요소를 반복해야 함(외부 반복) |

하나의 스트림에서는 딱 한번만 탐색할 수 있음

## Stream Operations (스트림 연산)

데이터 소스 → 중간 연산(파이프라인 구성) → 최종 연산(파이프라인 실행 및 결과 도출)

### 중간 연산

- 연산끼리 연결되어 파이프라인을 형성
- 또 다른 스트림 반환
- 최종 연산 수행 전까지 아무 연산도 수행되지 않음
- filter, sorted와 같은 연산

| 연산 | 반환 형식 | 인수 | 함수 디스크립터 |
| --- | --- | --- | --- |
| filter | Stream<T> | Predicate<T> | T → boolean |
| map | Stream<R> | Function<T, R> | T → R |
| limit | Stream<T> | Comparator<T> | (T, T) → int |
| sorted | Stream<T> | Comparator<T> | (T, T) → int |
| distinct | Stream<T> | Comparator<T> | (T, T) → int |

### 최종 연산

- 파이프라인을 실행한 다음 닫음
- 스트림 파이프라인에서 결과를 도출함

| 연산 | 반환 형식 | 목적 |
| --- | --- | --- |
| forEach | void | 스트림의 각 요소를 소비하면서 람다 적용 |
| count | long(generic) | 스트림 요소 개수 반환 |
| collect | - | 스트림을 리듀스해서 리스트, 맵, 정수 형식의 컬렉션을 만듬. |

## Filtering (필터링)

> Predicate(불리언을 반환하는 함수)를 인수로 받아 일치하는 **모든 요소**를 포함하는 스트림을 반환
.filter()
> 

```java
@Slf4j
@Component
public class Filtering {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Utilization / Filtering =====");
        List<Dish> menu = Dish.menu();

        // filter
        List<Dish> vegetarianMenu = menu.stream().filter(Dish::isVegetarian).collect(Collectors.toList());

        vegetarianMenu.forEach(m -> log.debug("Name : {}, Vegetarian : {}", m.getName(), m.isVegetarian()));

        // distinct
        List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 4, 2);

        List<Integer> distinctResult = numbers.stream().filter(i -> i % 2 == 0).distinct().collect(Collectors.toList());
        distinctResult.stream().forEach(r -> log.debug("result : {}", r.toString())); // 2, 4

    }
}
```

## Slicing (슬라이싱)

> 스트림의 요소를 선택하거나 스킵
> 

### Predicate

.takeWhile() : predicate가 거짓이되면 이전 요소 모두 반환

.dropWile() : predicate가 거짓이되면 남은 요소 모두 반환

```java
@Slf4j
@Component
public class Slicing {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Utilization / Slicing =====");
        List<Dish> menu = Dish.menu();

        menu.forEach(d -> log.debug("getName : {}, getCalories : {}", d.getName(), d.getCalories()));

        // takeWhile
        List<Dish> slicedMenu1 = menu.stream().takeWhile(d -> d.getCalories() < 320).collect(Collectors.toList());
        slicedMenu1.forEach(d -> log.debug("takeWile_getName : {}", d.getName()));

        // dropWhile : 조건과 다른 것들만 리턴
        List<Dish> slicedMenu2 = menu.stream().dropWhile(d -> d.getCalories() < 320).collect(Collectors.toList());
        slicedMenu2.forEach(d -> log.debug("dropwhile_getName : {}", d.getName()));
    }
}
```

### Stream Reduction

> .limit(n) : 최대 n개 요소 반환
> 

### Skip Element

> .skip(n) : 처음 n개 요소 제외
> 

## Mapping (매핑)

> 특정 데이터 매핑 및 변환
.map()
> 

### flatMap

> .flatMap() : 스트림의 각 값을 다른 스트림으로 만들고, 모든 스트림을 하나의 스트림으로 연결함
> 

```java
@Slf4j
@Component
public class Mapping {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Utilization / Mapping =====");

        List<Dish> menu = Dish.menu();

        List<String> dishNames = menu.stream()
            .map(Dish::getName)
            .collect(Collectors.toList());

        dishNames.forEach(d -> log.debug("getName() = {}", d));

        List<String> words = Arrays.asList("Modern", "Java", "In", "Action");
        List<Integer> wordLengths = words.stream()
            .map(String::length)
            .collect(Collectors.toList());
        wordLengths.forEach(d -> log.debug("wordLength= {}", d));

        //flatMap
        List<String> hwords = Arrays.asList("Hello", "World");
        List<String> hSplitWords = hwords.stream()
            .map(w -> w.split("")) // 개별 문자 배열로 변환
            .flatMap(Arrays::stream) // 생성된 스트림을 하나의 스트림으로 평면화
            .distinct()
            .collect(Collectors.toList());

        hSplitWords.stream().forEach(d -> log.debug("hSplitwords = {}", d));
        // H
        // e
        // l
        // o
        // ...

        List<Integer> numbers1 = Arrays.asList(1, 2, 3);
        List<Integer> numbers2 = Arrays.asList(3, 4);

        List<int[]> pairs = numbers1.stream()
            .flatMap(
                i -> numbers2.stream()
                    .map(j -> new int[]{i, j})
            )
            .collect(Collectors.toList());

        pairs.forEach(d -> log.debug("pairs = {}", d));
        // 1, 3
        // 1, 4
        // ...

        List<int[]> pairs2 = numbers1.stream()
            .flatMap(
                i -> numbers2.stream()
                    .filter(j -> (i + j) % 3 == 0)
                    .map(j -> new int[]{i, j})
            )
            .collect(Collectors.toList());

        pairs2.forEach(d -> log.debug("pairs2 = {}", d));
    }
}
```

## Search and Matching (검색)

### anyMatch

> 적어도 한 요소와 일치하는지 확인
> 

### Predicate가 모든 요소와 일치하는지 확인

allMatch() : 모든 요소가 주어진 Predicate와 일치하는지 확인

noneMatch() : 주어진 Predicate와 일치하는 요소가 없는지 확인

Short Circuit(쇼트서킷)

> 예를들어, AND 연산을 사용할 때 하나라도 거짓이면, 나머지 결과와 상관없이 전체 결과가 거짓이 되는 상황
> 

### Search element

### findAny

> 임의의 요소 반환
findFirst와 차이점이 없어 보이지만, 병렬 처리 시 가장 먼저 찾은 요소를 반환
> 

### findFirst

> 첫 번째 요소 반환
병렬 처리 시 Stream의 순서를 고려하여 가장 앞에 있는 요소 반환
> 

## Reducing (연산)

> 스트림 요소를 조합해서 복잡한 질의 표현
> 

### reduce

> reduce(초기값, 연산)
초기값이 없으면 Optional<>로 반환
> 

```java
@Slf4j
@Component
public class Searching {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Utilization / Searching =====");
        List<Dish> menu = Dish.menu();

        // match
        if (menu.stream().anyMatch(Dish::isVegetarian)) {
            log.debug("vegetarian이 하나 이상입니다."); // true
        }

        boolean result = menu.stream()
            .allMatch(dish -> dish.getCalories() < 1000);
        log.debug("result = {}", result); // true

        boolean result2 = menu.stream()
            .noneMatch(dish -> dish.getCalories() >= 1000);
        log.debug("result2 = {}", result2); // true

        // search
        menu.stream()
            .filter(Dish::isVegetarian)
            .findAny()
            .ifPresent(d -> log.debug("name= {}", d.getName()));

        menu.stream()
            .filter(Dish::isVegetarian)
            .findFirst()
            .ifPresent(d -> log.debug("name= {}", d.getName()));

        // reduce
        List<Integer> numbers = IntStream.range(1, 11).boxed().collect(Collectors.toList());
        Integer reduced = numbers.stream().reduce(0, (a, b) -> a + b);
        log.debug("reduced = {}", reduced); // 55

        int maxNumber = numbers.stream().reduce(0, Integer::max);
        log.debug("maxNumber = {}", maxNumber);
    }
}
```

## Practice !

```java
@RequiredArgsConstructor
@Getter
public class Trader {

    private final String name;
    private final String city;

    @Override
    public String toString() {
        return "Trader:" + this.name + " in " + this.city;
    }
}
```

```java
@RequiredArgsConstructor
@Getter
@ToString
public class Transaction {

    private final Trader trader;
    private final int year;
    private final int value;

    public static List<Transaction> transactions() {
        Trader trader1 = new Trader("Raoul", "Cambridge");
        Trader trader2 = new Trader("Mario", "Milan");
        Trader trader3 = new Trader("Alan", "Cambridge");
        Trader trader4 = new Trader("Brian", "Cambridge");

        return Arrays.asList(
            new Transaction(trader4, 2011, 300),
            new Transaction(trader1, 2012, 1000),
            new Transaction(trader1, 2011, 400),
            new Transaction(trader2, 2012, 710),
            new Transaction(trader2, 2012, 700),
            new Transaction(trader3, 2012, 950)
        );
    }
}
```

```java
@Component
@Slf4j
public class Answers {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Practice =====");

        List<Transaction> transactions = Transaction.transactions();

        List<Transaction> result1 = transactions.stream()
            .filter(t -> t.getYear() == 2011)
            .sorted(comparing(Transaction::getValue)).toList();
        result1.forEach(t -> log.debug("result = {}", t));

        List<String> result2 = transactions.stream()
            .map(t -> t.getTrader().getCity())
            .distinct()
            .toList();
        result2.forEach(t -> log.debug("result2 = {}", t));

        List<Trader> result3 = transactions.stream()
            .map(Transaction::getTrader)
            .filter(t -> t.getCity().equals("Cambridge"))
            .distinct()
            .sorted(comparing(Trader::getName))
            .toList();
        result3.forEach(t -> log.debug("result3 = {}", t));

        String result4 = transactions.stream()
            .map(t -> t.getTrader().getName())
            .distinct()
            .sorted()
            .reduce("", (x, y) -> x + "" + y);
        log.debug("result4 = {}", result4);

        if (transactions.stream().anyMatch(t -> t.getTrader().getCity().equals("Milan"))) {
            log.debug("result5 = Milan에 거래자가 있습니다.");
        }

        List<Integer> result6 = transactions.stream()
            .filter(t -> t.getTrader().getCity().equals("Cambridge"))
            .map(Transaction::getValue)
            .toList();
        result6.forEach(r -> log.debug("r = {}", r.toString()));

        Optional<Integer> result7 = transactions.stream()
            .map(Transaction::getValue)
            .reduce(Integer::max);
        log.debug("max = {}", result7);

        // .max()
        Optional<Transaction> result7t1 = transactions.stream()
            .max(comparing(Transaction::getValue));
        log.debug("min = {}", result7t1);

        Optional<Integer> result8 = transactions.stream()
            .map(Transaction::getValue)
            .reduce(Integer::min);
        log.debug("min = {}", result8);

        // .min()
        Optional<Transaction> result8t1 = transactions.stream()
            .min(comparing(Transaction::getValue));
        log.debug("min = {}", result8t1);

    }

}
```

## Numeric Stream (숫자형 스트림)

### Primitive Stream Specialization (기본형 특화 스트림)

> IntStream, DoubleStream, LongStream
> 

sum, max 와 같이 숫자 관련 리듀싱 연산 수행 메서드 제공

필요 시 객체 스트림으로 복원하는 기능 제공

박싱 과정에서 일어나는 효율성에만 관련 있고, 추가 기능은 제공하지 않음.

```java
@Slf4j
@Component
public class Numeric {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Numeric Stream =====");

        List<Dish> menu = Dish.menu();

        // Primitive Stream Specialization

        int calories = menu.stream()
            .mapToInt(Dish::getCalories) // InStream으로 변환
            .sum();

        OptionalInt maxCalories = menu.stream() // Optional 제공
            .mapToInt(Dish::getCalories)
            .max();

        int maxCaloriesWithDefault = menu.stream() // Optional + Default
            .mapToInt(Dish::getCalories)
            .max()
            .orElse(1);

        Stream<Integer> boxedStream = menu.stream()
            .mapToInt(Dish::getCalories)
            .boxed(); // 특화 스트림을 일반 스트림으로 변환

        // Range

        OptionalInt maxValue = IntStream.rangeClosed(1, 100)
            .max();
        log.debug("maxValue = {}", maxValue); // 100

        Stream<double[]> pythagoreanTriples = IntStream.rangeClosed(1, 100)
            .boxed()
            .flatMap(a -> IntStream.rangeClosed(a, 100)
                .mapToObj(b -> new double[]{a, b, Math.sqrt(a * a + b * b)})
                .filter(t -> t[2] % 1 == 0)
            );

        pythagoreanTriples.limit(5)
            .forEach(v -> log.debug("pythagoreanTriples ={}", v));
    }
}
```

## Generate Stream (스트림 생성)

스트림을 생성하는 방법

- empty stream
- nullable stream
- infinite stream
- value → stream
- array → stream
- file → stream

```java
@Slf4j
@Component
public class GenerateStream {

    @EventListener(ApplicationStartedEvent.class)
    public void main() {
        log.info("===== Stream / Generate Stream =====");

        // value to stream
        Stream<String> stream = Stream.of("Modern", "Java", "In", "Action");
        stream.map(String::toUpperCase)
            .forEach(v -> log.debug("value v ={}", v));

        // empty stream
        Stream<String> emptyStream = Stream.empty();

        // nullable Stream
        Stream<Object> values = Stream.ofNullable(System.getProperty("home"));
        values.forEach(v -> log.debug("nullable v = {}", v));

        // Array to Stream
        int[] numbers = {2, 3, 5, 7, 11, 13};
        int sum = Arrays.stream(numbers).sum();
        log.debug("sum = {}", sum);

        // File to Stream
        long uniqueWords = 0;
        try (Stream<String> lines = Files.lines(Paths.get("src/main/java/com/megazone/modern/Stream/GenerateStream/data.txt"), Charset.defaultCharset())) {
            uniqueWords = lines.flatMap(line -> Arrays.stream(line.split(" ")))
                .distinct()
                .count();
        } catch (IOException e) {

        }
        log.debug("uniqueWords = {}", uniqueWords);

        // Infinite Stream (Unbounded Stream)
        Stream.iterate(0, n -> n + 2)
            .limit(10) // 무한한 값을 출력하지 않도록 사용 권장
            .forEach(v -> log.debug("v = {}", v)); // 0, 2, 4, ...

        Stream.iterate(new int[]{0, 1}, t -> new int[]{t[1], t[0] + t[1]})
            .limit(10)
            .forEach(t -> log.debug("fibonacci = {}", t[0]));

        Stream.generate(Math::random)
            .limit(10)
            .forEach(v -> log.debug("random v = {}", v));

    }
}
```

data.txt

```java
a b c d e
f g h i j
```

---

boxed

> .boxed() : primitive 자료형을 Wrapper 클래스(객체)로 변환하는 메서드
int → Integer
>
